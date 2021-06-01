pragma solidity ^0.5.0;

import "./PupperCoin.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/crowdsale/Crowdsale.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/crowdsale/emission/MintedCrowdsale.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/crowdsale/validation/CappedCrowdsale.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/crowdsale/validation/TimedCrowdsale.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/crowdsale/distribution/RefundablePostDeliveryCrowdsale.sol";


//  CrowdSale contracts are inherited
contract PupperCoinSale is Crowdsale, MintedCrowdsale, CappedCrowdsale, TimedCrowdsale, RefundablePostDeliveryCrowdsale {
    using SafeMath for uint256;
    
    constructor(
        uint256 rate,            
        address payable wallet,  
        PupperCoin token,       
        uint256 goal,             
        uint256 open,     
        uint256 close      
    
    )
        Crowdsale(rate, wallet, token)
        MintedCrowdsale()
        CappedCrowdsale(goal)
        TimedCrowdsale(open, close)
        PostDeliveryCrowdsale()
        RefundableCrowdsale(goal)
        public
    {
        
    }
}

contract PupperCoinSaleDeployer {

    address public token_sale_address;
    address public token_address;

    constructor(
        string memory name,
        string memory symbol,
        address payable wallet, // this address will receive all Ether raised by the sale
        uint goal
    )
       public
    {
        // PupperCoin is created 
        PupperCoin token = new PupperCoin(name, symbol, 0);
        token_address = address(token);
        
        // PupperCoinSale is created, with token, goal, and open and close times are set 
        
        PupperCoinSale pupper_sale = new PupperCoinSale(1, wallet, token, goal, now, now + 24 weeks);
        
        
        token_sale_address = address(pupper_sale);
        
        // PupperCoinSale contract is set to minter then its is renounced 
        token.addMinter(token_sale_address);
        token.renounceMinter();
    }
}