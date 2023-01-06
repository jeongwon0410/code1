// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;
import "hardhat/console.sol";

contract BContract {
    struct Candidate {
        uint index;
        string nickName;
        string comment;
        string url;
        string imgUrl;
        uint point;
      
    }

    Candidate[] candidates;
    
    struct Voter {
        uint originNft;
        uint nft;
        bool isVote;
    }

    address[] votersAddress;
    mapping(address => Voter) voters;


    address owner;
    mapping(address => bool) admin;
    address[] adminsAdress;
    
    constructor() {
        owner = msg.sender;
    }

    modifier register {
        require(admin[msg.sender] == true);
        _;
    }

    function setAdmin(address _addr) private {
        
        require(owner == msg.sender);
        admin[_addr] = true;
        adminsAdress.push(_addr);
    }

    function makeAdmin(address _addr) public {
        setAdmin(_addr);
    }

    function getAdmin() public view returns(address[] memory){
        return adminsAdress;
    }

    function setCandidata(string memory _nickName, string memory _comment, string memory _url, string memory _imgUrl) public register {
        candidates.push(Candidate(candidates.length+1,_nickName,_comment,_url,_imgUrl,0));
       
    }

    function getCandidate() public view returns(Candidate[] memory){
        return candidates;
    }


    function getVoter(address _addr) public view returns(Voter memory){
        return voters[_addr];
    }

    function vote(uint index, address _vaddr, uint _amount) public {

        require(_amount > 0 ,"error1");
        if(voters[_vaddr].isVote == false ){
            votersAddress.push(_vaddr);
            voters[_vaddr].originNft = _amount;
            voters[_vaddr].nft = _amount;
            voters[_vaddr].isVote = true;
        }else{
            if(_amount == voters[_vaddr].originNft){
                require(voters[_vaddr].nft > 0,"error2");
            }else if(_amount > voters[_vaddr].originNft){
                voters[_vaddr].nft += _amount - voters[_vaddr].originNft ;
                voters[_vaddr].originNft = _amount;
            }else if(_amount < voters[_vaddr].originNft){
                voters[_vaddr].nft -= voters[_vaddr].originNft - _amount;
                voters[_vaddr].originNft = _amount;
                require(voters[_vaddr].nft > 0,"error3");
            }
        }
       
        candidates[index].point++;

        voters[_vaddr].nft--;
    }


    function init() public register {
        
        delete candidates;

        for(uint i=0;i<votersAddress.length;i++){
            delete voters[votersAddress[i]];
        }
        delete votersAddress;
        
    }



}
