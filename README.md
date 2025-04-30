# Amazon Nova Sonic Demo: Hotel Reservation System

This example demonstrates a practical customer service use case for Amazon Nova Sonic model, implementing a hotel reservation system. The application enables natural conversational interactions through a web interface, allowing customers to creat new reservations, look up existing reservations, and cancel reservations through voice commands while interacting with an AI assistant.

The system showcases how businesses can leverage Amazon Nova Sonic model to create seamless, human-like customer service experiences for handling hotel reservation system, including creating new reservations, looking up existing reservations,policy explanations, refund calculations, and confirmation processes.

## Key Features

- **Real-time Voice Interaction**: Bidirectional audio streaming for natural conversations using Amazon Nova Sonic model
- **Complete Reservation Management**: Create new reservations, look up existing reservations, and process cancellations
- **Reservation Verification**: Tool for looking up reservation details by name and check-in date
- **Dynamic Cancellation Policies**: Different refund calculations based on timing and reservation type (full, partial, or no refund)
- **Confirmation Workflow**: Explicit confirmation required before processing cancellations for customer protection
- **Refund Calculation**: Automatic calculation of refund amounts based on cancellation policies and timing
- **Confirmation Codes**: Generation of unique confirmation codes for both new reservations and cancellations
- **Session Management**: Automatic cleanup of inactive sessions to optimize resource usage
- **Responsive Web Interface**: User-friendly interface with chat history and real-time audio processing
- **Tool Integration**: Seamless integration with backend reservation systems through structured tool calls

## Usage Instructions

### Prerequisites
- Node.js (v14 or higher)
- AWS Account with Bedrock access
- AWS CLI configured with appropriate credentials
- Modern web browser with WebAudio API support

**Required packages:**

```json
{
  "dependencies": {
    "@aws-sdk/client-bedrock-runtime": "^3.785",
    "@aws-sdk/client-bedrock-agent-runtime": "^3.782",
    "@aws-sdk/credential-providers": "^3.782",
    "@smithy/node-http-handler": "^4.0.4",
    "@smithy/types": "^4.1.0",
    "@types/express": "^5.0.0",
    "@types/node": "^22.13.9",
    "axios": "^1.6.2",
    "dotenv": "^16.3.1",
    "express": "^4.21.2",
    "pnpm": "^10.6.1",
    "rxjs": "^7.8.2",
    "socket.io": "^4.8.1",
    "ts-node": "^10.9.2",
    "uuid": "^11.1.0"
  }
}
```

### Installation
1. Clone the repository:
```bash
git clone <repository-url>
cd <repository-name>
```

2. Install dependencies:
```bash
npm install
```

3. Configure AWS credentials:
```bash
# Configure AWS CLI with your credentials
aws configure --profile bedrock-test
```

4. Build the TypeScript code:
```bash
npm run build
```

### Quick Start
1. Start the server:
```bash
npm start
```

2. Open your browser:
```
http://localhost:3000
```

3. Grant microphone permissions when prompted.

### Sample Conversation Flow

Here's an example of how a conversation might flow, showcasing both cancellation and new reservation creation in under 10 exchanges:

1. **Customer**: "Hi, I need to cancel my current reservation and make a new one."
2. **Agent**: "Hello! I'd be happy to help with both. Could you please provide your name and check-in date for the reservation you want to cancel?"
3. **Customer**: "My name is Angela Park, check-in date April 12, 2025."
4. **Agent**: "Thank you, Angela. I found your reservation at Seaview Hotel for a Deluxe Ocean View room from April 12-15. You're eligible for a full refund if cancelled by April 5, or 50% until April 10. Would you like to proceed with cancellation?"
5. **Customer**: "Yes, please cancel and I'd like to book a new reservation at Mountain Lodge from May 20-25 for a Standard King room."
6. **Agent**: "I've processed your cancellation. Your confirmation code is CANX-123456 with a full refund of $750.00 processing in 5-7 business days. For your new reservation, a Standard King at Mountain Lodge from May 20-25 will be $950.00. Would you like to confirm this booking?"
7. **Customer**: "Yes, please book it."
8. **Agent**: "Perfect! I've created your new reservation. Your confirmation code is CONF-789012. Your Mountain Lodge Standard King room is confirmed for May 20-25, 2025, total $950.00. Is there anything else you need help with today?"

### Troubleshooting
1. Microphone Access Issues
- Problem: Browser shows "Permission denied for microphone"
- Solution: 
  ```javascript
  // Check if microphone permissions are granted
  const permissions = await navigator.permissions.query({ name: 'microphone' });
  if (permissions.state === 'denied') {
    console.error('Microphone access is required');
  }
  ```

2. Audio Playback Issues
- Problem: No audio output
- Solution:
  ```javascript
  // Verify AudioContext is initialized
  if (audioContext.state === 'suspended') {
    await audioContext.resume();
  }
  ```

3. Connection Issues
- Check server logs for connection status
- Verify WebSocket connection:
  ```javascript
  socket.on('connect_error', (error) => {
    console.error('Connection failed:', error);
  });
  ```