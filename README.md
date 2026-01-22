# BDOP-Token
Main repository for the Base
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/*
    Token Name: BDOP
    Symbol: BDOP
    Total Supply: 1,000,000,000
    Decimals: 18
    Owner can burn tokens
*/

contract BDOP {
    string public name = "BDOP";
    string public symbol = "BDOP";
    uint8 public decimals = 18;

    uint256 public totalSupply;
    address public owner;

    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event Burn(address indexed from, uint256 value);

    constructor() {
        owner = msg.sender;

        uint256 initialSupply = 1_000_000_000 * 10 ** uint256(decimals);
        totalSupply = initialSupply;
        balanceOf[owner] = initialSupply;

        emit Transfer(address(0), owner, initialSupply);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function transfer(address to, uint256 value) public returns (bool) {
        require(balanceOf[msg.sender] >= value, "Insufficient balance");
        balanceOf[msg.sender] -= value;
        balanceOf[to] += value;
        emit Transfer(msg.sender, to, value);
        return true;
    }

    function approve(address spender, uint256 value) public returns (bool) {
        allowance[msg.sender][spender] = value;
        emit Approval(msg.sender, spender, value);
        return true;
    }

    function transferFrom(address from, address to, uint256 value) public returns (bool) {
        require(balanceOf[from] >= value, "Insufficient balance");
        require(allowance[from][msg.sender] >= value, "Not allowed");

        allowance[from][msg.sender] -= value;
        balanceOf[from] -= value;
        balanceOf[to] += value;

        emit Transfer(from, to, value);
        return true;
    }

    // 🔥 Burn function
    function burn(uint256 value) public onlyOwner {
        require(balanceOf[msg.sender] >= value, "Not enough tokens");
        balanceOf[msg.sender] -= value;
        totalSupply -= value;

        emit Burn(msg.sender, value);
        emit Transfer(msg.sender, address(0), value);
    }
}
