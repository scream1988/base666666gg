# base666666ggfrom web3 import Web3

RPC_URL = "https://mainnet.base.org"

# USDC on Base (official)
USDC = Web3.to_checksum_address("0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913")

# keccak256("Transfer(address,address,uint256)")
TRANSFER_TOPIC = "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef"

USDC_DECIMALS = 6


def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))
    if not w3.is_connected():
        raise RuntimeError("❌ Cannot connect to Base RPC")

    latest = w3.eth.block_number
    print("✅ Connected to Base")
    print("Latest block:", latest)

    logs = w3.eth.get_logs(
        {
            "fromBlock": latest,
            "toBlock": latest,
            "address": USDC,              # filter ONLY USDC logs
            "topics": [TRANSFER_TOPIC],   # filter ONLY Transfer events
        }
    )

    print(f"USDC Transfer logs in block {latest}: {len(logs)}")

    for i, log in enumerate(logs[:50], start=1):
        from_addr = "0x" + log["topics"][1].hex()[-40:]
        to_addr = "0x" + log["topics"][2].hex()[-40:]
        amount_raw = int.from_bytes(log["data"], byteorder="big")
        amount_usdc = amount_raw / (10 ** USDC_DECIMALS)

        print(f"\n#{i}")
        print("From :", Web3.to_checksum_address(from_addr))
        print("To   :", Web3.to_checksum_address(to_addr))
        print("USDC :", amount_usdc)


if __name__ == "__main__":
    main()
