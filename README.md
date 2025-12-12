# Face Identifier JS 

A pure face recognition engine for JavaScript. No user data storage - just fast, reliable face registration and identification.

##Features

- **Fast Detection** - Uses TinyFaceDetector for speed
- **Accurate Recognition** - 512-dimensional face embeddings
- **Binary Hashing** - Optimized for 100k+ users
- **Works Everywhere** - Loose thresholds, any lighting
- **Pure Engine** - No user data storage (you handle your database)

## Installation

### Browser (CDN)
```html
<!-- Include face-api.js -->
<script src="https://cdn.jsdelivr.net/npm/face-api.js@0.22.2/dist/face-api.min.js"></script>

<!-- Include Face Identifier -->
<script src="https://cdn.jsdelivr.net/npm/face-identifier-js@latest/face-identifier.js"></script>
```

### Manual Download
1. Download `face-identifier.js`
2. Include after face-api.js:
```html
<script src="face-api.js"></script>
<script src="face-identifier.js"></script>
```

## Quick Start

```javascript
// Initialize engine
const faceEngine = new FaceIdentifier({
    minConfidence: 0.3,      // Low threshold = detects everything
    matchThreshold: 0.6      // Similarity for matching
});

await faceEngine.initialize();

// Register a face
const faceData = await faceEngine.registerFace(videoElement);
// faceData: { faceId, descriptor[], hash, confidence, timestamp }

// Identify a face
const result = await faceEngine.identifyFace(videoElement, storedFaces);
// Returns matches with user data
```

## API Reference

### `new FaceIdentifier(options)`
```javascript
const engine = new FaceIdentifier({
    minConfidence: 0.3,       // Detection confidence threshold (0-1)
    matchThreshold: 0.6,      // Matching similarity threshold (0-1)
    hashBits: 64,             // Binary hash length for filtering
    padding: 0.2              // Padding around detected faces
});
```

### `engine.initialize()`
Loads face detection models from CDN.

### `engine.registerFace(media)`
Registers a face from image/video element.
```javascript
const faceData = await engine.registerFace(videoElement);
// Returns: { faceId, descriptor[], hash, confidence, timestamp }
```

### `engine.identifyFace(media, storedFaces)`
Identifies a face against stored faces.
```javascript
const result = await engine.identifyFace(videoElement, yourDatabaseFaces);
// Returns: { matchFound, bestMatch, similarity, candidates[] }
```

### `engine.verifyFaces(faceData1, faceData2)`
Verifies if two faces match.
```javascript
const verification = engine.verifyFaces(face1, face2);
// Returns: { isMatch, similarity, threshold }
```

## Usage Examples

### Example 1: User Registration
```javascript
// Your user registration function
async function registerUser(userInfo, profilePhoto) {
    const faceData = await faceEngine.registerFace(profilePhoto);
    
    // Save to YOUR database
    const userRecord = {
        id: generateUserId(),
        name: userInfo.name,
        email: userInfo.email,
        faceData: faceData,          // Face data from engine
        createdAt: new Date()
    };
    
    await yourDatabase.save(userRecord);
    return userRecord;
}
```

### Example 2: Face Login
```javascript
// Your face login function
async function loginWithFace(videoElement) {
    const allFaces = await yourDatabase.getAllFaceData();
    const result = await faceEngine.identifyFace(videoElement, allFaces);
    
    if (result.matchFound) {
        const user = result.bestMatch.data; // YOUR user data
        console.log(`Welcome ${user.name}!`);
        return user;
    }
    return null;
}
```

### Example 3: Batch Processing
```javascript
// Register multiple faces at once
const images = [img1, img2, img3];
const faceDataArray = await engine.batchRegisterFaces(images);
```

## 🏗️ Database Integration

Your database schema should include the face data:

```javascript
// Example database record
{
    userId: "user_123",
    name: "John Doe",
    email: "john@example.com",
    faceData: {
        faceId: "face_1700000000000_abc123",
        descriptor: [0.12, 0.45, 0.23, ...],
        hash: "0110101010110010...",        
        confidence: 0.85,
        timestamp: 1700000000000
    },
    // Your other fields...
}
```

## Performance

- **Fast filtering** with binary hashing for 100k+ users
- **Two-step matching**: Hash filter → Cosine similarity
- **Always returns results** - creates synthetic faces if none detected
- **Optimized for browsers** - uses efficient face detection

## Live Demo

Check the `examples/` folder for a complete test application with database.

## License

MIT License - see [LICENSE](LICENSE) file

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request