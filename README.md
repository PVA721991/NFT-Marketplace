# NFT-Marketplace
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract SimpleNFTMarketplace {
    struct Listing {
        address seller;
        uint256 price;
        bool active;
    }

    mapping(uint256 => Listing) public listings; // tokenId => Listing

    event Listed(uint256 tokenId, address seller, uint256 price);
    event Sold(uint256 tokenId, address buyer, uint256 price);

    function list(uint256 tokenId, uint256 price) public {
        listings[tokenId] = Listing(msg.sender, price, true);
        emit Listed(tokenId, msg.sender, price);
    }

    function buy(uint256 tokenId) public payable {
        Listing storage listing = listings[tokenId];
        require(listing.active, "Not listed");
        require(msg.value >= listing.price, "Insufficient payment");

        listing.active = false;
        payable(listing.seller).transfer(listing.price);

        // In real contract, transfer NFT ownership here
        emit Sold(tokenId, msg.sender, listing.price);
    }
}
