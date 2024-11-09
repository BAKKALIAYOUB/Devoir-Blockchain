# MiniSocial Smart Contract Documentation

## Contract Overview

MiniSocial is a decentralized social media platform built on Ethereum that enables users to create, manage, and interact with posts in a secure and transparent manner.

## Smart Contract Features

### 1. Data Structures

#### Post Structure
```solidity
struct Post {
    string message;       // Content of the post
    address author;       // Address of post creator
    uint256 createdAt;   // Timestamp of creation
    uint256 updatedAt;   // Timestamp of last update
    uint256 likes;       // Number of likes
    uint256 dislikes;    // Number of dislikes
    bool isDeleted;      // Deletion status
}
```

#### User Interaction Structure
```solidity
struct UserInteraction {
    bool hasLiked;       // Has user liked the post
    bool hasDisliked;    // Has user disliked the post
}
```

### 2. State Variables

```solidity
Post[] public posts;                     // Array of all posts
mapping(address => uint256[]) public userPosts;  // User's posts mapping
mapping(uint256 => mapping(address => UserInteraction)) public userInteractions;  // Post interactions
uint256 public constant MAX_POST_LENGTH = 500;   // Maximum post length
```

### 3. Events

```solidity
event PostAdded(uint256 indexed postId, address indexed author, string message, uint256 timestamp);
event PostModified(uint256 indexed postId, string newMessage, uint256 timestamp);
event PostLiked(uint256 indexed postId, address indexed user, uint256 newLikeCount);
event PostDisliked(uint256 indexed postId, address indexed user, uint256 newDislikeCount);
event PostDeleted(uint256 indexed postId, address indexed author, uint256 timestamp);
```

### 4. Core Functions

#### Post Creation
```solidity
function publishPost(string memory _message) public
```
- Creates new post with provided message
- Validates message length
- Emits PostAdded event
- Associates post with creator's address

#### Post Interaction
```solidity
function likePost(uint256 postId) public
function dislikePost(uint256 postId) public
```
Features:
- One-time like/dislike per user
- Automatic unlike when disliking and vice versa
- Updates interaction counters
- Emits appropriate events

#### Post Management
```solidity
function modifyPost(uint256 postId, string memory _newMessage) public
function deletePost(uint256 postId) public
```
Features:
- Only author can modify/delete their posts
- Updates timestamp on modification
- Soft deletion mechanism
- Event emission for tracking

#### Post Retrieval
```solidity
function getPost(uint256 index) public view returns (
    string memory message,
    address author,
    uint256 likes,
    uint256 dislikes,
    uint256 createdAt,
    uint256 updatedAt,
    bool hasLiked,
    bool hasDisliked
)
```
Features:
- Returns complete post details
- Includes user's interaction status
- Validates post existence

### 5. Security Features

#### Modifiers
```solidity
modifier validPostId(uint256 postId)     // Ensures post exists and isn't deleted
modifier onlyAuthor(uint256 postId)      // Restricts to post author
modifier validMessage(string memory)      // Validates message content
```

#### Security Implementations:
1. **Double Voting Prevention**
   - Tracks user interactions
   - Prevents multiple likes/dislikes
   - Handles vote switching

2. **Access Control**
   - Author-only post modification
   - Author-only post deletion
   - Public read access

3. **Input Validation**
   - Message length limits
   - Post existence checks
   - Deletion status validation

### 6. Advanced Features

#### User Post Management
```solidity
function getUserPostsWithDetails(address user) public view returns (
    uint256[] memory postIds,
    string[] memory messages,
    uint256[] memory likes,
    uint256[] memory dislikes,
    uint256[] memory timestamps
)
```
Features:
- Retrieves all active posts for a user
- Includes complete post details
- Filters out deleted posts
- Optimized for gas efficiency

### 7. Gas Optimization Features

1. **Storage Optimization**
   - Efficient struct packing
   - Minimal storage operations
   - Use of memory for temporary data

2. **Batch Operations**
   - Combined data retrieval
   - Optimized array operations
   - Efficient mapping usage

### 8. Error Handling

The contract implements comprehensive error handling:
```solidity
require(postId < posts.length, "Post does not exist");
require(!posts[postId].isDeleted, "Post has been deleted");
require(posts[postId].author == msg.sender, "Only the author can perform this action");
require(bytes(_message).length > 0, "Message cannot be empty");
require(bytes(_message).length <= MAX_POST_LENGTH, "Message is too long");
```

### 9. Usage Examples

#### Creating a Post
```solidity
// Create a new post
minisocial.publishPost("Hello, Web3!");
```

#### Interacting with Posts
```solidity
// Like a post
minisocial.likePost(0);

// Dislike a post
minisocial.dislikePost(0);

// Modify own post
minisocial.modifyPost(0, "Updated message");

// Delete own post
minisocial.deletePost(0);
```

#### Retrieving Posts
```solidity
// Get single post
let postDetails = minisocial.getPost(0);

// Get user's posts
let userPosts = minisocial.getUserPostsWithDetails(userAddress);
```

### 10. Future Improvements

Potential enhancements for future versions:
1. Comment system
2. Post categories/tags
3. User profiles
4. Content moderation
5. Token integration
6. Enhanced privacy features
7. Post sharing mechanism
8. Advanced search capabilities

This contract serves as a foundation for a decentralized social media platform, prioritizing security, efficiency, and user experience while maintaining full transparency on the blockchain.