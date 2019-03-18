pragma solidity ^0.4.16;
 
contract ERC721Token {
  //Non-fungible Token
  //与ERC20兼容的接口
  function balanceOf(address _owner) public constant returns (uint256 balance);  
  //所有权相关的接口
  function transfer(address _to, uint256 _token) public returns (bool success);  
  function transferFrom(address _from, address _to, uint256 _token) public returns (bool success);
  function approve(address _spender, uint256 _token) public returns (bool success);
  function allowance(address _owner, address _spender) public constant returns (uint256 remaining);  
  function ownerOf(uint256 _tokenId) public returns (address owner);  
  function tokensOfOwner(address _owner) public returns(uint256[] ownerTokens);  
  //元数据接口,可选
  function tokenMetadata(uint256 _tokenId) public constant returns (string infoUrl);  
  //事件
  event Transfer(address indexed _from, address indexed _to, uint256 _token);  
  event Approval(address indexed _owner, address indexed _spender, uint256 _token);
  
}
 
contract TokenDemo is ERC721Token {
 
  string  public name = "Test Token";    
  string  public symbol = "TEST";		 
  uint8   public decimals = 0;
  uint256 public INITIAL_SUPPLY = 100 * (10 ** uint256(decimals));  
  
  address minter;
 
  mapping (address => uint256) balances;
  mapping (address => mapping (address => uint256)) allowed;
 
  //token structure 
  struct Token {			//users can change structure for their dapp				
      string property;     
  }
 
  Token[] tokens;
 
  mapping (uint256 => address) public tokenIndexToOwner;
  mapping (address => uint256) ownershipTokenCount;
  mapping (uint256 => address) public tokenIndexToApproved;  
  mapping (uint256 => string) tokenLinks;  //元数据，例如IPFS哈希或HTTP(S)链接
 
  function TokenDemo() public {
	  minter = msg.sender;
  }
 
  //通证元数据接口
  function tokenMetadata(uint256 _tokenId) public constant returns (string infoUrl){
	  return tokenLinks[_tokenId];
  }
  
  function getProperty(uint256 _tokenId) public returns (string property){
      return tokens[_tokenId].property;
  }
 
  function totalSupply() public returns (uint) {
      return tokens.length;
  }
 
  function ownerOf(uint256 _tokenId)  public returns (address owner) {
      owner = tokenIndexToOwner[_tokenId];
      require(owner != address(0));
  }
 
  function tokensOfOwner(address _owner) public returns(uint256[] ownerTokens) {
      uint256 tokenCount = balanceOf(_owner);
 
      if (tokenCount == 0) {
          return new uint256[](0);
      } else {
          uint256[] memory result = new uint256[](tokenCount);
          uint256 totalTokens = totalSupply();
          uint256 resultIndex = 0;
 
          uint256 tokenId;
          for (tokenId = 0; tokenId < totalTokens; tokenId++) {
              if (tokenIndexToOwner[tokenId] == _owner) {
                  result[resultIndex] = tokenId;
                  resultIndex++;
              }
          }
          return result;
      }
  }
 
  function _transfer(address _from, address _to, uint256 _tokenId) internal {
      ownershipTokenCount[_to]++;
      tokenIndexToOwner[_tokenId] = _to;
      if (_from != address(0)) {
          ownershipTokenCount[_from]--;
          delete tokenIndexToApproved[_tokenId];
      }
      
      Transfer(_from, _to, _tokenId);	// Emit the transfer event
  }
 
  function createToken(string _property, address _owner) public returns (uint) { 	
	  require(msg.sender == minter);		//only minter can create token		
  
      Token memory token = Token({
	    property: _property
      });
	
      var newTokenId = tokens.push(token) - 1;
      require(newTokenId == uint256(uint32(newTokenId)));
      _transfer(0, _owner, newTokenId);
      return newTokenId;
  }
 
  function _owns(address _claimant, uint256 _tokenId) internal view returns (bool) {
      return tokenIndexToOwner[_tokenId] == _claimant;
  }
 
  function transfer(address _to, uint256 _tokenId) public returns (bool) {
      require(_to != address(0));
      require(_to != address(this));
      require(_owns(msg.sender, _tokenId));
      _transfer(msg.sender, _to, _tokenId);
  }
 
  function transferFrom(address _from, address _to, uint256 _tokenId) public returns (bool success) {
      require(_to != address(0));
      require(_to != address(this));
      require(_approvedFor(msg.sender, _tokenId));
      require(_owns(_from, _tokenId));
      _transfer(_from, _to, _tokenId);
  }
 
  function balanceOf(address _owner) public view returns (uint256 count) {
      return ownershipTokenCount[_owner];
  }
 
  function _approvedFor(address _claimant, uint256 _tokenId) internal view returns (bool) {
      return tokenIndexToApproved[_tokenId] == _claimant;
  }
 
  function _approve(uint256 _tokenId, address _approved) internal {
      tokenIndexToApproved[_tokenId] = _approved;
  }
 
  function approve(address _to, uint256 _tokenId) public returns (bool success) {
      require(_owns(msg.sender, _tokenId));
      _approve(_tokenId, _to);
      Approval(msg.sender, _to, _tokenId);
  }
 
  function allowance(address _owner, address _spender) public constant returns (uint256 remaining) {
      return allowed[_owner][_spender];
  }
  
}
