<script lang="ts">
	import Icon from 'svelte-awesome/components/Icon.svelte';
	import {
		user,
		shoppingCart,
		mapMarker,
		calendar,
		instagram,
		facebook,
		linkedin,
		twitter
	} from 'svelte-awesome/icons';
	import { onMount } from 'svelte';
	import QRCode from 'qrcode';

	export let data;

	let transactionID: string;
	let CAcode: string;
	let imgurl: string;
	let amount = 1280;
	let accountNumber = '007010100273206';
	let ifscCode = 'UTIB0000007';
	let success = false;
	let qrcodeurl = `upi://pay?pa=${accountNumber}@${ifscCode}.ifsc.npci&am=${amount}&cu=INR`;

	QRCode?.toDataURL(`${qrcodeurl}`, function (err: any, url: string) {
		imgurl = url;
	});

	onMount(() => {
		let bgimage = window.document.getElementById('img');
		if (bgimage !== null) {
			bgimage.style.backgroundImage = `url(${imgurl})`;
		}
	});

	const verify = (CAcode: string) => {
		let warning = (document.getElementById('warning'));
		if (CAcode.length !== 6) {
			if (warning != null) {
				warning.style.visibility = 'visible';
				warning.innerHTML = 'Please enter a 6 digit CA-Code';
			}
			setTimeout(() => {
				if (warning != null) {
				warning.style.visibility = 'hidden';
			}
			}, 3500);
		} else {
			fetch('https://testpetri.onrender.com/api/events/verify', {
				method: 'POST',
				body: JSON.stringify({
					CAcode
				}),
				headers: {
					'Content-type': 'application/json; charset=UTF-8'
				}
			})
				.then((response) => response.json())
				.then((data) => {
					console.log(data)
					console.log('a')
					success = data.verified;
					let verify = window.document.getElementById('verify');
					let CAcodeElt = window.document.getElementById('CAcode')  as HTMLInputElement;
					if (success) {
						amount = 1000;
						qrcodeurl = `upi://pay?pa=${accountNumber}@${ifscCode}.ifsc.npci&am=${amount}&cu=INR`;

						QRCode?.toDataURL(`${qrcodeurl}`, function (err: any, url: any) {
							imgurl = url;
						});

						let bgimage = window.document.getElementById('img');
						if (bgimage !== null) {
							bgimage.style.backgroundImage = `url(${imgurl})`;
						}

						if(verify != null){
							verify.innerHTML = 'Verified';
							CAcodeElt.disabled = true;
						}
					}
					else{
						if(verify != null){
							verify.innerHTML = 'Unverified';
							verify.style.backgroundColor = 'red';
						}
						setTimeout(()=>{
							if(verify != null){
								verify.innerHTML = 'Verify';
								verify.style.backgroundColor = 'rgb(17, 184, 17)';
							}
						},2000)
					}
				});
		}
	};

	const submit = () => {
		CAcode = success ? CAcode : '';
		if (transactionID) {
			fetch('https://testpetri.onrender.com/api/events/apply', {
				method: 'POST',
				body: JSON.stringify({
					transactionID,
					CAcode
				}),
				headers: {
					'Content-type': 'application/json; charset=UTF-8'
				}
			})
				.then((response) => response.json())
				.then((data) => console.log(data));
		} else {
			let warning = /** @type {HTMLInputElement} */ document.getElementById('warning');
			if (warning != null) {
				warning.style.visibility = 'visible';
				warning.innerHTML = 'Please enter your transaction id';
			}
			setTimeout(() => {
				if (warning != null) {
				warning.style.visibility = 'hidden';
			}
			}, 3500);
		}
	};
</script>

<div id='all'>
<h1 style="text-align:center;margin-top:10rem;">
	Registering for <span style="color: blueviolet;">{data.name}</span>
</h1>

<div class="form">
	<div style="background-color: rgb(90, 14, 137,0.3);" class="payment">
		<div style="display: inline-block;">
			<p
				style="margin-bottom: 1rem;margin-top: 0.8rem;font-size:155%;display: inline-block;font-family:monospace"
			>
				Scan here for payment
			</p>
			<div id="img" />
			<p style="margin-top: 1rem;font-size:155%;box-sizing:border-box;font-family:cursive">
				<span style="font-family:cursive">&#8377;</span>{amount}
			</p>
			<svg
				style="margin-top: -1rem;"
				xmlns="http://www.w3.org/2000/svg"
				width="120"
				height="50"
				fill-rule="evenodd"
				><path d="M95.678 42.9L110 29.835l-6.784-13.516z" fill="#097939" /><path
					d="M90.854 42.9l14.322-13.065-6.784-13.516z"
					fill="#ed752e"
				/><path
					d="M22.41 16.47l-6.03 21.475 21.407.15 5.88-21.625h5.427l-7.05 25.14c-.27.96-1.298 1.74-2.295 1.74H12.31c-1.664 0-2.65-1.3-2.2-2.9l6.724-23.98zm66.182-.15h5.427l-7.538 27.03h-5.58zM49.698 27.582l27.136-.15 1.81-5.707H51.054l1.658-5.256 29.4-.27c1.83-.017 2.92 1.4 2.438 3.167L81.78 29.49c-.483 1.766-2.36 3.197-4.19 3.197H53.316L50.454 43.8h-5.28z"
					fill="#747474"
				/></svg
			>
		</div>
	</div>
	<div id="data">
		<p style="color: #FCF3FF;margin-left:1rem;margin-bottom:0.5rem">Transaction Id</p>
		<input
			id="transId"
			type="text"
			maxlength="25"
			placeholder="Your Transaction Id"
			style="display:block;"
			bind:value={transactionID}
			required
		/>
		<p style="color: #FCF3FF;margin-left:1rem;margin-bottom:0.5rem">CA Code</p>
		<div class="code_verification" style="">
			<input
				id="CAcode"
				type="text"
				placeholder="Your CA Code(if any)"
				maxlength="6"
				style="margin-top:5px;background-color:black !important;border:black"
				bind:value={CAcode}
			/>
			<button id="verify" style="cursor:pointer" on:click={() => verify(CAcode)}
				>Verify</button
			>
		</div>
		<div id="submitButton">
			<p id="warning">Hii</p>
			<input
				id="submit"
				on:click={() => submit()}
				type="submit"
				style="cursor:pointer; display:block; margin-top:5px;"
			/>
		</div>
	</div>
</div>
</div>

<style>
	#all{
		display: block;
	}
	.form {
		margin-left: 10rem;
		margin-top: 4rem;
		padding-top: 0rem;
		margin-bottom: 10rem;
		
	}
	.payment {
		margin-right: 15rem;
		height: 23rem;
		width: 20rem;
		text-align: center;
		background-color: black;
		float: right;
		padding: 2.5rem;
		padding-top: 0.7rem;
		border-radius: 10%;
		z-index: 1;
		position: relative;
		margin-top: 0rem;
	}
	* {
		box-sizing: border-box;
	}
	#img {
		display: inline-block;
		height: 10rem;
		padding: 5rem;
		background-size: cover;
		width: 10rem;
	}
	#submitButton {
		width: 25rem;
		margin-top: 1rem;
	}
	#warning {
		color: red;
		height: 0.8rem;
		margin-top: -0.5rem;
		text-align: center;
		visibility: hidden;
	}
	#submitButton > input {
		width: 6.5rem;
		height: 2.5rem;
		font-size: large;
		border-radius: 25%/50%;
		background-color: rgb(0, 183, 255);
		border: none;
		display: block;
		margin: 0 auto;
	}
	.code_verification {
		display: flex;
		text-align: center;
		justify-content: center;
		background-color: black;
		width: 25rem;
		height: 3.5rem;
		border: 2px solid grey;
		border-radius: 10%/70%;
		padding-left: 1rem;
	}
	#transId {
		border: 2px solid grey;
		background-color: black;
		padding-left: 1.5rem;
		width: 25rem;
		height: 3.5rem;
		border-radius: 10%/70%;
		color: #fcf3ff;
		outline: none;
	}
	#verify {
		background-color: rgb(17, 184, 17);
		color: #fcf3ff;
		border: none;
		height: 1.5rem;
		padding: 0.3rem;
		display: flex;
		text-align: center;
		justify-content: center;
		width: 5rem;
		position: relative;
		margin: auto 0;
		margin-right: 1rem;
		margin-left: 3rem;
		border-radius: 20%/50%;
		/* line-height: 1.5rem; */
	}
	#CAcode {
		width: 18rem;
		height: 2.5rem;
		padding-left: 0.5rem;
		color: #fcf3ff;
		outline: none;
	}
	input:-webkit-autofill,
	input:-webkit-autofill:hover, 
	input:-webkit-autofill:focus{
		transition: background-color 5000s ease-in-out 0s;
		-webkit-text-fill-color: #fcf3ff;
	}
	#data {
		width: 30rem;
		display: inline-block;
		z-index: 2;
		margin-top: 1rem;
		margin-left: 10rem;
	}
</style>
