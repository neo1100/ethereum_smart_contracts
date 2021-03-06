### contract address
https://etherscan.io/address/0x02ffebe131d09f0ffaf607df5a9884cf1902f5f5#code

### code with vulnerabilities
```
function mintToken (address target, uint256 amount) onlycfo public returns (bool) {
    require (target != address(0x0));
    require (amount != 0);
    balanceOf[target] += amount;
    totalSupply += amount;
    emit MintToken (target, amount);
    return true;
}
```

The mintToken function of ERC20TokenTC is vulnerable to the integer overflow vulnerability. The balance of arbitrary address could be manipulated hostilely by the contract creator. After adding 'amount' to the 'balanceOf[target]', the result value are not checked carefully which could cause an integer overflow.

### PoC

The exploit of this vulnerability use the same method as described in [expoit of MyAdvancedToken](https://github.com/neo1100/ethereum_smart_contracts/tree/master/0x02dc6487991227a5cd580f88e6c32f560649d03d_MyAdvancedToken). Below is the exploit method of that vulnverability.

#### step 1
Call mintToken(), assigning 1000 tokens to the address 0x692a70d2e424a56d2c6c27aa97d1a86395877b3a. After the call, the balance of the target address is 1000.</br>
![integer_overflow_1_1.png](png/integer_overflow_1_1.png "integer_overflow_1_1.png")

#### step 2
Call mintToken() again, now assigning 2**256 - 999 (which is 115792089237316195423570985008687907853269984665640564039457584007913129638937) to the target address. After the call, the balance of the target address is 1.</br>
![integer_overflow_1_2.png](png/integer_overflow_1_2.png "integer_overflow_1_2.png")

The balance of the target address is tampered with by the contract creater.
