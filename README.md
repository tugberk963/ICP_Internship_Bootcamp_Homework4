# DIP721 NFT 

## Overview
This example demonstrates implementing an NFT canister.

The canister is a basic implementation of the standard.

We will mint basic sample RPG Character NFT.

### 1. Installation

-   [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
-   [x] Download and install [git.](https://git-scm.com/downloads)
-   [x] `wasm32-unknown-unknown` targets; these can be installed with `rustup target add wasm32-unknown-unknown`.

### 2. Functionality 

#### Initialization and Upgrade
-   **`init(args: InitArgs)`**: Initializes the canister with given arguments.
-   **`pre_upgrade()`**: Prepares and serializes the canister's state before an upgrade.
-   **`post_upgrade()`**: Restores the canister's state after an upgrade.

#### NFT Management
-   **`mint(to: Principal, metadata: MetadataDesc, blob_content: Vec<u8>)`**: Mints a new NFT.
#### NFT Transfer
-   **`transfer_from(from: Principal, to: Principal, token_id: u64)`**: Transfers an NFT from one principal to another.
-   **`safe_transfer_from(from: Principal, to: Principal, token_id: u64)`**: Safely transfers an NFT, checking for zero addresses.
-   **`transfer_from_notify(from: Principal, to: Principal, token_id: u64, data: Vec<u8>)`**: Transfers an NFT and notifies the recipient.
-   **`safe_transfer_from_notify(from: Principal, to: Principal, token_id: u64, data: Vec<u8>)`**: Safely transfers an NFT with notification.
  
#### Query Functions
-   **`balance_of(user: Principal)`**: Returns the number of NFTs owned by a user.
-   **`owner_of(token_id: u64)`**: Returns the owner of a specific NFT.
-   **`name()`**: Returns the name of the NFT collection.
-   **`symbol()`**: Returns the symbol of the NFT collection.
-   **`total_supply()`**: Returns the total number of NFTs minted.
-   **`supported_interfaces()`**: Lists the supported interfaces (DIP721 standards).
-   **`get_metadata(token_id: u64)`**: Retrieves metadata for a specific NFT.
-   **`get_metadata_for_user(user: Principal)`**: Retrieves metadata for all NFTs owned by a user.
-   **`is_principal_member(user: Principal)`**: Returns if user is a member (this is for example implementation).
   
#### Customization Functions
-   **`set_name(name: String)`**: Sets the name of the NFT collection.
-   **`set_symbol(sym: String)`**: Sets the symbol of the NFT collection.
-   **`set_logo(logo: Option<LogoResult>)`**: Sets the logo for the NFT collection.

### 3.Testing Process

#### Starting
Be sure our project have required node modules.
```bash
cd ICP_Internship_Bootcamp_Homework4\
npm i
```
then we can continue as follows : 
```bash
dfx start --background --clean
```

#### Deploying Canister
With entering following command to command line. We can deploy our nft canister.
General Paramater Info:
- `name`: Name of the NFT Collection. Can't be empty.
- `symbol`: Short description - symbol of the NFT Collection. Can't be empty.
- `custodians`: List of users who can manage canister.
- `logo`: Logo of the NFT Collection. Can be null.
```bash
  dfx deploy dip721_nft_container --argument \
  "(record {
      name = \"rpgCharacters\";
      symbol = \"CHR\";
      logo = opt record {
          data = \"$(base64 -i ./logo.png)\";
          logo_type = \"image/png\";
      };
      custodians = opt vec { principal \"$(dfx identity get-principal)\" };
  })"
```
Our example uses the example logo in the project folder. To deploy without logo : 
```bash
dfx deploy --argument \
"(record {
    name = \"DesiredCharacterName\";
    symbol = \"CHR\";
    logo = null;
    custodians = opt vec { principal \"$(dfx identity get-principal)\" };
})"
```
<img src="https://github.com/tugberk963/ICP_Internship_Bootcamp_Homework4/blob/master/deployment.gif">

## Minting NFT with Desired Metadata

First we need to know our identity.
```bash
dfx identity get-principal
```
Output will look like this : 
```bash
to3u4-pcqg5-x6qva-2xu7f-3a7ad-nqntf-om75i-kebtk-pprha-t35t4-uae
```
Now we can use our identity for our principal paramater as below, and mint our nft with following metadata.
```bash
dfx canister call dip721_nft_container mintDip721 \
    "(principal\"to3u4-pcqg5-x6qva-2xu7f-3a7ad-nqntf-om75i-kebtk-pprha-t35t4-uae\",vec{record{
        purpose=variant{Rendered};
        data=blob\"hello\";
        key_val_data=vec{
            record{
                \"characterName\";
                variant{TextContent=\"DesiredCharacterName\"};
            };
            record{
                \"characterLevel\";
                variant{Nat8Content=99:nat8}
            };
            record{
                \"characterClass\";
                variant{TextContent=\"Warrior\"};
            };
        }
    }},blob\"hello\")"
```
If output looks like below, our minting process is done. Now lets see the changes.
```bash
(variant { Ok = record { id = 0 : nat; token_id = 0 : nat64 } })
```
<img src="https://github.com/tugberk963/ICP_Internship_Bootcamp_Homework4/blob/master/minting.gif">

## Getting Metadata 
With the following command, we can see our NFT's metadata.
```bash
dfx canister call dip721_nft_container getMetadataDip721 '(0:nat64)'
```
Output will look like below : 
```bash
(
  variant {
    Ok = vec {
      record {
        data = blob "hello";
        key_val_data = vec {
          record { "characterClass"; variant { TextContent = "Warrior" } };
          record {
            "characterName";
            variant { TextContent = "DesiredCharacterName" };
          };
          record { "characterLevel"; variant { Nat8Content = 99 : nat8 } };
        };
        purpose = variant { Rendered };
      };
    }
  },
)
```
<img src="https://github.com/tugberk963/ICP_Internship_Bootcamp_Homework4/blob/master/checkingmetada.gif">

