# Solana NFT Tutorial

If you're not in Solana, it's time for you be in it, because just recently [Solana overtakes Ethereum in Daily NFT trading volume](https://decrypt.co/101342/solana-overtakes-ethereum-daily-nft-trading-trippin-ape-tribe). This means that you can be certain that NFTs are thriving in Solana.

So if you're looking to figure out how to create [NFTs](https://www.alchemy.com/nfts) with Solana, then you came to the right place.

# A Guide on CLI Format

For this tutorial I will be placing CLI commands within code blocks like below with `# shell` at the top to indicate that it's a CLI command. I will also denote the output of the command in a separate code block labeled with `# output`.

```sh
# shell
echo "this is a cli command"
```

```sh
# output
this is a cli command
```

# Project Template

In order to make the process of going through the tutorial easier, I have made a template with different folders that will serve as your workspaces and you can get it [over here](git@github.com:kquirapas/solana-nft-tutorial.git).
```
/solana-nft-tutorial
	/0-assets
		/Head
			HotPink.png
			Pink.png
			Yellow.png
		/Mouth
			Circular.png
			Oval.png
			Wonky.png
		/Eyes
			Frown.png
			Smile.png
			Shock.png
	/1-generate
	/2-build
	/3-deploy
```

Each folder would correspond to a different workspace for cloning, installing, generating, and deploying.

# Solana Tool Suite

### Installation

Before we jump into building our NFT collection we must first install Solana's suite of tools by following [this guide](https://docs.solana.com/cli/install-solana-cli-tools).

Upon installing, make sure that it's properly installed by running the command below:

```sh
# shell
solana --version
```

You must receive a similar output below:
```sh
# output
solana-cli 1.10.24 (src:15456493; feat:2982808786)
```

### Basic Usage

Solana's tool suite is designed to have [multiple commands](https://docs.solana.com/cli/conventions) we can use to deploy our NFT collection. However, for this tutorial we will only go through the crucial steps that you need in order to get started.

> **WARNING**: The first thing you should always do is configure your Solana work environment to make sure you don't deploy, use some of your Solana.

#### Quick list

`solana --version` - get the current version of the Solana tool suite installed

`solana config get` - get the current Solana tool suite configuration.

`solana config set` - set the current Solana tool suite configuration.

`solana-keygen new` - create a new filesystem keypair.

`solana airdrop` - airdrop some devnet / testnet Solana in a certain address.

#### First Steps

The first thing you will be doing when you first install Solana is to test whether it's installed properly with `solana --version`.

The next thing you should **always do** is to check the current configuration of your Solana tool suite with `solana config get`.

```sh
# shell
solana config get
```

You should receive an output similar to the one below. The key things to look out for are the `RPC URL` and your `Keypair Path`. These 2 are the ones you will most likely change in order to do some testing.
```sh
# output
Config File: /home/kirby/.config/solana/cli/config.yml
RPC URL: https://api.mainnet-beta.solana.com
WebSocket URL: wss://api.mainnet-beta.solana.com/ (computed)
Keypair Path: /home/kirby/.config/solana/id.json
Commitment: confirmed
```

Notice that the configuration above is in `mainnet-beta`. In order for us to do some testing, we must change this to `devnet`.

```sh
# shell
solana config set --url devnet
```

You should get a similar output below.
```sh
# output
Config File: /home/kirby/.config/solana/cli/config.yml
RPC URL: https://api.devnet.solana.com
WebSocket URL: wss://api.devnet.solana.com/ (computed)
Keypair Path: /home/kirby/.config/solana/id.json
Commitment: confirmed
```

Now you're done getting ready for development!

# Generating Assets with HashLips

### Setup
Now that you're done installing Solana's tool suite, it's time to generate some  assets with **HashLips**. In order to get started get a copy of [our template here](git@github.com:kquirapas/solana-nft-tutorial.git) and clone it locally with the command below.
```sh
# shell
git clone *insert repository link here*
```

You should receive directory structure similar to below with the pre-made layer images we will be using for the art generation we will be doing with HashLips.
```
/solana-nft-tutorial
	/0-assets
		/Head
			HotPink.png
			Pink.png
			Yellow.png
		/Mouth
			Circular.png
			Oval.png
			Wonky.png
		/Eyes
			Frown.png
			Smile.png
			Shock.png
	/1-generate
	/2-build
	/3-deploy
```

Change directory into `1-generate` and this will serve as your workspace for generating your art.
```sh
# shell
cd 1-generate
```

Once in `1-generate` let's get a copy of [HashLips and install it locally](https://github.com/HashLips/hashlips_art_engine).
```sh
# shell
git clone https://github.com/HashLips/hashlips_art_engine.git
```

Go to the root of the newly cloned repository and install the dependencies.
```sh
# shell
yarn install # yarn
npm install # npm
```

### Usage

HashLips is a very powerful art engine that allows you to create different configurations for your art generation, but for this tutorial's purposes **we will keep it as simple as possible**.

After doing the steps above you must be left with a similar set of files.
```sh
# shell
ls # list files in current directory
```

```sh
# output
banner.png  layers    modules       package-lock.json  utils
constants   LICENSE   node_modules  README.md          yarn.lock
index.js    logo.png  package.json  src
```

Change directory into `layers` and remove all the sample files.
```sh
# shell
cd layers
rm -r * # delete all in directory 
```

Copy all of the folders inside `0-assets` in the template into `layers` folder because that's where all of our layers will be placed for art generation using **HashLips**.

After copy and pasting the folders from `0-assets` your `layers` folder should look like the one below.
```
/layers
	/Head
		HotPink.png
		Pink.png
		Yellow.png
	/Mouth
		Circular.png
		Oval.png
		Wonky.png
	/Eyes
		Frown.png
		Smile.png
		Shock.png
```

Now go into the `src` folder and modify `config.js` to your needs. For this tutorial we will keep it simple and only modify the necessary ones below:

- `network` 
- `namePrefix` and `description`
- `solanaMetadata`
- `layerConfigurations`

First things first, modify your `network` from `NETWORK.eth` to `NETWORK.sol`.
```js
const network = NETWORK.sol
```

Change your `namePrefix` to the name of your collection. In this case we're calling it **Patrick Head**. Also change `description` to the description of your NFT collection.
```js
const namePrefix = "Patrick Head";
const description = "My Patrick Heads";
```

Modify your Solana Token Metadata as needed. If you want to know more about Solana's token metadata standard, you can check the [Metaplex Docs](https://docs.metaplex.com/programs/token-metadata/token-standard).

This is the default Solana metadata in `config.js`.
```js
const solanaMetadata = {
  symbol: "YC",
  seller_fee_basis_points: 1000, // Define how much % you want from secondary market sales 1000 = 10%
  external_url: "https://www.youtube.com/c/hashlipsnft",
  creators: [
    {
      address: "7fXNuer5sbZtaTEPhtJ5g5gNtuyRoKkvxdjEjEnPN4mC",
      share: 100,
    },
  ],
};
```

Here's a brief overview of the following
