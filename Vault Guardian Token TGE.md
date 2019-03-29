On *November 14th, 2018* the Vault Guardian Token (**VGT**) was deployed on the Ethereum Mainnet. Here is a summary of the deployment process and key concepts.

# Primary Contracts

*   VGT Contract `0xCc394f10545AeEf24483d2347B32A34a44F20E6F`: this is the main contract defining the ERC20 VGT token.
*   Vesting Contract `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672`: this contract governs vesting over time of tokens locked for early investors, company advisors, and the yearly refill of the advisory pool.
*   Vault12 Locked Tokens Contract `0xd698dC82f4Cd43097009E5DDC4e0ADC1E43875a3`: a contract that governs 25 years of token vesting to be incrementally issued into Vault12 ownership.

# VGT Transfers

## Direct transfers

Direct investors participated in funding rounds without timing conditions. They received their tokens directly in the following transactions, totaling **183,322,140** tokens. Investors paid between 143.1962µETH - 480.9958µETH or between $0.085 - $0.1 USD per token in this group.

#### Transactions list

*   0xe035aedd6f30cfa10550f628c658e71d78fa34e2c25e08b1e870746a8ac181ac
*   0x5952a38e27e4af60b1d73f76d9d1809691375a41b9b9d5a0638f800fca0832ef
*   0x6631d355285f7bc7ffed4b1f8b538ea400e0946faca4bd45032c69aad15491c2
*   0x5c3f2bf286b77bd9e74eecf5532591f705f598677d2ed5f58b20c5bb927d1e88
*   0x19651f52d82ee9d9b161c402814ae93c49fb316f6730116547079827ed06b2fe
*   0x8bbbd9ef5b5b44a603b8fa688c2601abdc849cbeedefe7b625e76eccee53dae8
*   0x8e880647d688b349ca815b894df7ac18129cb77866169877f0ddd95c5c2d06d8
*   0x75ad26fdadca57f9245a8fa06a5d1599872616cad36584aa51e68246721696c1
*   0x7f74ba14b18f6027b54f17e09723b4d4ee04dbde2ba063881e9067a178cda462
*   0x5318199eb55e1b6db32361fc6cecd04e4b96db15ce43d8e8020127922d1b76ac
*   0xec3879324855a231ce1678dfb892f1ce903ed7cae74cafa50cc284c5649e239b
*   0x6afef4400dc06dd809b123b1599f29669e87a0dc8ee3c28e8e424fd91fa235ca
*   0xc3e5d116a943be068b22efe5e2651e5a90abd70d6461aeab186eddef056c2b01
*   0x7b22f80ff72cd076ddcbe2c8ea5ef4c42d2db8018228bd2132adeb79a7357599

## Locked Investors

Investors who participated in our earliest round received the best price per token. These investors had to accept a one year lock on their tokens as a condition of participation. This one year hold is implemented via the vesting contract `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672` in the following transactions. The total of **13,742,854** tokens will remain locked until May 15, 2019. Investors paid between 97.2188µETH - 119.1602µETH or 8.0267µBTC or $0.07 USD per tokens. 

#### Transactions list
*   0x13465995fd7053ecc55fc9b7875d01e92e75f4595424ebafce0562e08fb89007
*   0x19c26bf69388b687138898b903e7eb1829c85c01bf292e5b12f769c3d74ad52b
*   0x423b0379aa99ea9ba03526ec1a9eed1308ea85d854781817a860186a451fd6ec
*   0x16ef3205a2363d92115935086c6eea55cfa1c191ac2904b98a8e0bfc48e16902
*   0xfd90da85e72385ead08cc6cd26c5598063c1f8ef209151abdd12a969755a9748
*   0x0f4fe6c734a7ff8a3d81bdedef7b6db7a9b197cdbc586aacfba3871a1dd9b052
*   0x462145f755638ec62c40e4e790b2f83ff426c4277d3772662d42cf3c0ef0a5ad

The contract at [https://etherscan.io/address/0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672#code](https://etherscan.io/address/0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672#code) uses the `calculateGrantClaim` function to determine the number of tokens a recipient can claim at the current date based on their grant start date, total amount and vesting cliff. One year lock ups are implemented as 365 day grants with a 364 day cliff.

    function calculateGrantClaim(uint256 _grantId) public view returns (uint256, uint256) {
	Grant storage tokenGrant = tokenGrants[_grantId];

	// For grants created with a future start date, that hasn't been reached, return 0, 0
	if (currentTime() < tokenGrant.startTime) {
		return (0, 0);
	}

	// Check cliff was reached
	uint elapsedTime = currentTime().sub(tokenGrant.startTime);
	uint elapsedDays = elapsedTime.div(SECONDS_PER_DAY);

	if (elapsedDays < tokenGrant.vestingCliff) {
		return (elapsedDays, 0);
	}

	// If over vesting duration, all tokens vested
	if (elapsedDays >= tokenGrant.vestingDuration) {
		uint256 remainingGrant = tokenGrant.amount.sub(tokenGrant.totalClaimed);
		return (tokenGrant.vestingDuration, remainingGrant);
	} else {
		uint256 daysVested = elapsedDays.sub(tokenGrant.daysClaimed);
		uint256 amountVestedPerDay = tokenGrant.amount.div(uint256(tokenGrant.vestingDuration));
		uint256 amountVested = uint256(daysVested.mul(amountVestedPerDay));
		return (daysVested, amountVested);
	}
    }

### Late Locked Investors

A few locked investors did not provide their funding address in by our TGE deadline, and therefore their **5,197,643** will remain in a staging address. Once those investors provide us with their final addresses, these tokens will be sent to the vesting contract `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672` and allocated to their locked investor addresses.

## Advisory and Community Rewards

The advisors and community rewards pool is located at `0x77AE3FDD2F9feA1B4f32034a6250CbeDE57396ab`. Each year 20MM tokens are unlocked to be allocated to new Vault12 advisors and community grants. 15MM tokens from the first year's budget are allocated in various advisory grants at `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672` and the last 2MM will be granted from the advisory pool shortly, bringing the remainder of `0x77AE3FDD2F9feA1B4f32034a6250CbeDE57396ab` account to 5MM tokens, which is the reminder of advisory/community balance for the 2018-2019 first year of operations.

The rewards pool yearly refill is implemented via 4 yearly vesting grants that last one year from their start date, and grant the balance of tokens on the last day in the following transactions:

*   20MM vesting starting from _5/14/2018_ into the advisory pool `0x082a9b4a683f25396a3db677cd2adb38e246eea20d0962113e82d590c53ec2d8`
*   20MM vesting starting from _5/15/2019_ into the advisory pool `0x8358c2890cdc8245ef0fa96da1082a22cf3cfa69f5ee463f6131e35d9b806316`
*   20MM vesting starting from _5/14/2020_ into the advisory pool `0x589fd125ec00bea94e665d879454b1e85d7d7d3fc62bccc5cf736598fa081bc0`
*   20MM vesting starting from _5/14/2021_ into the advisory pool `0x49656ec1eae2911475aaa090d781e5ef9b2945537ab2fac24bce9ccb3b2aee87`

## Vault12 Accounts

The vast majority of all VGT tokens are locked in a **25 year** vesting contract at `0xd698dC82f4Cd43097009E5DDC4e0ADC1E43875a3` transfered at tx `0xf3a587a3fe4f345dd2d4b7fe2a2a4371ba326786f7676e60b32c14b3d2d8e02c`. This contract currently holds a balance of **647,737,363** tokens. These tokens will be issued per terms described in Vault12 whitepaper at rate of 10% of remaning balance each year. In the contract [https://etherscan.io/address/0xd698dc82f4cd43097009e5ddc4e0adc1e43875a3#code](https://etherscan.io/address/0xd698dc82f4cd43097009e5ddc4e0adc1e43875a3#code) the function `calculateGrantClaim` calculates yearly grant available for claiming at given date:

    function calculateGrantClaim(address _recipient) public view returns (uint256, uint256) {
	Grant storage tokenGrant = tokenGrants[_recipient];

	// For grants created with a future start date, that hasn't been reached, return 0, 0
	if (currentTime() < tokenGrant.startTime) {
		return (0, 0);
	}

	uint256 elapsedTime = currentTime().sub(tokenGrant.startTime);
	uint256 elapsedYears = elapsedTime.div(SECONDS_PER_YEAR);

	// If over vesting duration, all tokens vested
	if (elapsedYears >= tokenGrant.vestingDuration) {
		uint256 remainingGrant = tokenGrant.amount.sub(tokenGrant.totalClaimed);
		uint256 remainingYears = tokenGrant.vestingDuration.sub(tokenGrant.yearsClaimed);
		return (remainingYears, remainingGrant);
	} else {
		uint256 i = 0;
		uint256 tokenGrantAmount = tokenGrant.amount;
		uint256 totalVested = 0;
		for(i; i < elapsedYears; i++){
			totalVested = (tokenGrantAmount.mul(10)).div(100).add(totalVested);
			tokenGrantAmount = tokenGrant.amount.sub(totalVested);
		}
		uint256 amountVested = totalVested.sub(tokenGrant.totalClaimed);
		return (elapsedYears, amountVested);
	}
}</pre>

The single grant existing inside that contract is a 25 year vesting contract for the *Vault12 Deposit* account at `0x6080E870943134D78fC5bdA619aA808748Edf764`. As years go by, the *Vault12 Deposit* will be able to obtain and transfer 10% of the remainder each anniversary of timestamp `1530403200`.

The Vault12 Deposit accounts holds an initial balance of 50MM tokens reserved for next year's product launch and promotional activities, such as potential AirDrops to the first users who create their Vaults in the released application.

# Token Distribution Summary

*   Twenty five year lock in `0xd698dC82f4Cd43097009E5DDC4e0ADC1E43875a3`: **647,737,363** tokens
*   Direct transfers to unlocked investors:  **183,322,140**tokens
*   Early investors one year lock till _May 15 2019_ in `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672`: **13,742,854** tokens
*   Unclaimed locked investors: **5,197,643** tokens
*   Granted and upcoming advisory grants via `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672`: **15,000,000** tokens
*   Remaning advisory balance till _May 15, 2019_ on `0x77AE3FDD2F9feA1B4f32034a6250CbeDE57396ab`: **5,000,000** tokens
*   Vault12 Deposit account at `0x6080E870943134D78fC5bdA619aA808748Edf764`: **50,000,000** tokens
*   4x advisory pool refresh grants arriving in 2019-22 at `0xd6d79F85D8Cb962b15181AAC0c1545D61B6c5672`: **80,000,000**tokens
*   Total created VGT tokens: **1,000,000,000**.
*   Price range paid per tokens: 97.2188µETH - 119.1602µETH | 8.0267µBTC | $0.07 USD | 143.1962µETH - 480.9958µETH | $0.085 - $0.1 USD.
*   Total current supply of tokens unlocked by vesting conditions: **183,322,140**
*   Total supply of tokens after _May 15, 2019_: **270,036,373**
    *   First year balance: **183,322,140**
    *   Unlocked investors: **13,742,854**
    *   Unclaimed: **5,197,643**
    *   Vault12 unlock: **64,773,736**
    *   Advisory vesting: **3,000,000**