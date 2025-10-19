# Posts

Posts are the core content type on dX. Anyone with a connected wallet can create, publish, and monetize posts on the platform.

## Creating a Post

### 1. Content Creation
Posts are created using a powerful rich-text editor that supports:
- **Text Formatting**: Headers, paragraphs, bold, italic, underline
- **Rich Media**: Images with captions, embedded videos, code blocks
- **Advanced Blocks**: Tables, lists, quotes, delimiters, warnings
- **Inline Tools**: Markers, inline code, links

### 2. Saving Drafts
- Posts can be saved as drafts to IPFS before publishing
- Drafts are stored in private IPFS groups
- Only the author can access draft content
- Drafts can be edited and updated multiple times
- Each save generates a new Content Identifier (CID)

### 3. Publishing Process
When you're ready to publish:

1. **Add Publishing Metadata**:
   - Title (required)
   - Thumbnail Image (optional but recommended)
   - Description (brief summary, max 256 characters)
   - Price (in ETH, can be 0 for free posts)

2. **Upload Thumbnail**: 
   - Thumbnail is uploaded to IPFS
   - Publicly accessible for preview cards

3. **Blockchain Transaction**:
   - Metadata submitted to DXmaster smart contract
   - Creates a unique ERC20 token (DXasset) for the post
   - Stores: CID, title, thumbnail CID, description, price
   - Content CID is recorded on-chain

4. **Asset Creation**:
   - Each post becomes a unique asset with its own contract address
   - Author receives initial token supply
   - Token represents ownership/access rights

## Post Visibility

### Public Metadata
The following information is publicly visible for all posts:
- Title
- Description
- Thumbnail image
- Author's wallet address
- Price
- Asset contract address

### Private Content
- Full post content remains private on IPFS
- Only accessible to:
  - The author (creator)
  - Users who have purchased/subscribed to the post
  - Free posts (price = 0) are accessible to everyone

## Subscription Model

### How Subscriptions Work
1. Users browse posts by viewing public metadata
2. To access full content, they must "subscribe" (purchase access)
3. Payment is sent to the smart contract
4. User receives DXasset tokens representing ownership
5. Ownership is verified before content is served

### Pricing
- Creators set individual prices for each post
- Prices are in ETH (Wei precision)
- Minimum price: 0 (free posts)
- No maximum limit

### Lifetime Access
- Subscription is one-time payment
- No recurring fees
- Ownership is permanent (unless token is transferred)
- Verified through blockchain ownership records

## Post Discovery

### Homepage
- All published posts appear on the homepage
- Newest posts shown first
- Filter by:
  - All Posts
  - Free Posts only
- Search by title
- Pagination for performance

### Post Preview
When viewing a post without ownership:
- See full metadata (title, description, thumbnail)
- View author information
- See price and purchase button
- Cannot access full content

When viewing as owner:
- Full content displayed
- Can read entire post
- All embedded media accessible

## Technical Details

### IPFS Storage
- Content stored using Pinata service
- Private groups ensure access control
- CIDs are deterministic and permanent
- Gateway URL configured in environment

### Smart Contract Integration
- Each post creates a DXasset contract (ERC20)
- DXmaster tracks all assets
- Ownership verified through balanceOf checks
- Transactions immutable on Sepolia testnet

### Authentication
- Wallet signatures verify author identity
- JWT tokens for API authentication
- No email or personal data required
- Re-authentication required periodically (2 hour token expiry)

