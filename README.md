# Project-Aura
# WAZARD Pro - WhatsApp Automation Chrome Extension

## Project Structure
```
wazard-pro/
├── manifest.json
├── package.json
├── webpack.config.js
├── public/
│   ├── icons/
│   │   ├── icon16.png
│   │   ├── icon48.png
│   │   ├── icon128.png
│   │   └── wazard-logo.svg
│   └── index.html
├── src/
│   ├── background/
│   │   ├── index.js
│   │   ├── messaging.js
│   │   ├── scheduler.js
│   │   └── analytics.js
│   ├── content/
│   │   ├── index.js
│   │   ├── injector.js
│   │   ├── whatsapp-api.js
│   │   └── utils.js
│   ├── popup/
│   │   ├── index.js
│   │   ├── App.jsx
│   │   ├── components/
│   │   │   ├── Dashboard.jsx
│   │   │   ├── ContactManager.jsx
│   │   │   ├── MessageBuilder.jsx
│   │   │   ├── Settings.jsx
│   │   │   ├── LogPanel.jsx
│   │   │   ├── AIAssistant.jsx
│   │   │   └── common/
│   │   │       ├── Button.jsx
│   │   │       ├── Input.jsx
│   │   │       ├── Tabs.jsx
│   │   │       ├── Toggle.jsx
│   │   │       └── Dropdown.jsx
│   │   └── styles/
│   │       └── tailwind.css
│   ├── services/
│   │   ├── database.js
│   │   ├── csvParser.js
│   │   ├── spinText.js
│   │   ├── aiService.js
│   │   └── authService.js
│   └── utils/
│       ├── throttle.js
│       ├── validator.js
│       ├── logger.js
│       └── constants.js
└── .gitignore
```

## manifest.json
```json
{
  "manifest_version": 3,
  "name": "WAZARD Pro - WhatsApp Automation",
  "version": "1.0.0",
  "description": "Advanced WhatsApp automation and messaging tool with AI assistance",
  "permissions": [
    "storage",
    "tabs",
    "activeTab",
    "scripting",
    "unlimitedStorage"
  ],
  "host_permissions": [
    "https://web.whatsapp.com/*"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "index.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "content_scripts": [
    {
      "matches": ["https://web.whatsapp.com/*"],
      "js": ["content.js"]
    }
  ],
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}
```

## package.json
```json
{
  "name": "wazard-pro",
  "version": "1.0.0",
  "description": "Advanced WhatsApp automation and messaging tool with AI assistance",
  "main": "index.js",
  "scripts": {
    "start": "webpack --watch --progress --config webpack.config.js",
    "build": "webpack --config webpack.config.js",
    "test": "jest"
  },
  "author": "WAZARD Team",
  "license": "MIT",
  "dependencies": {
    "@headlessui/react": "^1.7.17",
    "@heroicons/react": "^2.0.18",
    "axios": "^1.6.2",
    "dexie": "^3.2.4",
    "firebase": "^10.6.0",
    "papaparse": "^5.4.1",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-dropzone": "^14.2.3",
    "react-router-dom": "^6.19.0",
    "react-toastify": "^9.1.3",
    "uuid": "^9.0.1"
  },
  "devDependencies": {
    "@babel/core": "^7.23.3",
    "@babel/preset-env": "^7.23.3",
    "@babel/preset-react": "^7.23.3",
    "autoprefixer": "^10.4.16",
    "babel-loader": "^9.1.3",
    "copy-webpack-plugin": "^11.0.0",
    "css-loader": "^6.8.1",
    "html-webpack-plugin": "^5.5.3",
    "jest": "^29.7.0",
    "postcss": "^8.4.31",
    "postcss-loader": "^7.3.3",
    "style-loader": "^3.3.3",
    "tailwindcss": "^3.3.5",
    "webpack": "^5.89.0",
    "webpack-cli": "^5.1.4"
  }
}
```

## webpack.config.js
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  mode: 'development',
  devtool: 'cheap-module-source-map',
  entry: {
    popup: path.resolve('src/popup/index.js'),
    background: path.resolve('src/background/index.js'),
    content: path.resolve('src/content/index.js')
  },
  output: {
    path: path.resolve('dist'),
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react']
          }
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader', 'postcss-loader']
      },
      {
        test: /\.(png|jpg|jpeg|gif|svg)$/,
        type: 'asset/resource'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve('public/index.html'),
      filename: 'index.html',
      chunks: ['popup']
    }),
    new CopyWebpackPlugin({
      patterns: [
        { from: 'public/icons', to: 'icons' },
        { from: 'manifest.json', to: 'manifest.json' }
      ]
    })
  ],
  resolve: {
    extensions: ['.js', '.jsx']
  }
};
```

## public/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>WAZARD Pro</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

## src/background/index.js
```javascript
import { setupMessageListener } from './messaging';
import { initScheduler } from './scheduler';
import { initAnalytics } from './analytics';

// Initialize background services
const init = async () => {
  console.log('WAZARD Pro background service initializing...');
  
  // Initialize analytics tracking
  await initAnalytics();
  
  // Initialize message scheduler
  await initScheduler();
  
  // Setup message listeners
  setupMessageListener();
  
  // Register installation and update handlers
  chrome.runtime.onInstalled.addListener(handleInstalled);
};

// Handle extension installation or update
const handleInstalled = (details) => {
  if (details.reason === 'install') {
    // First time installation
    chrome.storage.local.set({
      installDate: new Date().toISOString(),
      messagesSent: 0,
      messagesQueued: 0,
      isFirstRun: true,
      messagingSettings: {
        defaultDelay: 3000,
        randomizeDelay: true,
        recoveryAttempts: 3,
        throttleLimit: 50
      }
    });
    
    // Open onboarding tab
    chrome.tabs.create({ url: 'https://wazard-pro.com/welcome' });
  } else if (details.reason === 'update') {
    // Extension updated
    const currentVersion = chrome.runtime.getManifest().version;
    chrome.storage.local.set({ 
      lastUpdated: new Date().toISOString(),
      currentVersion
    });
  }
};

// Initialize the background service
init();
```

## src/background/messaging.js
```javascript
import { scheduleMessages, getQueueStatus, pauseQueue, resumeQueue } from './scheduler';

// Setup message listener for communication between components
export const setupMessageListener = () => {
  chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
    console.log('Background received message:', message.type);
    
    switch (message.type) {
      case 'QUEUE_MESSAGES':
        scheduleMessages(message.payload.contacts, message.payload.template, message.payload.settings)
          .then(result => sendResponse({ success: true, queueId: result.queueId }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      case 'GET_QUEUE_STATUS':
        getQueueStatus(message.payload.queueId)
          .then(status => sendResponse({ success: true, status }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      case 'PAUSE_QUEUE':
        pauseQueue(message.payload.queueId)
          .then(() => sendResponse({ success: true }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      case 'RESUME_QUEUE':
        resumeQueue(message.payload.queueId)
          .then(() => sendResponse({ success: true }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      case 'CHECK_WHATSAPP_STATUS':
        checkWhatsAppStatus()
          .then(status => sendResponse({ success: true, status }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      default:
        sendResponse({ success: false, error: 'Unknown message type' });
        return false;
    }
  });
};

// Check if WhatsApp is properly loaded
const checkWhatsAppStatus = async () => {
  const tabs = await chrome.tabs.query({ url: 'https://web.whatsapp.com/*' });
  
  if (tabs.length === 0) {
    return { ready: false, reason: 'WhatsApp tab not found' };
  }
  
  try {
    const response = await chrome.tabs.sendMessage(tabs[0].id, { type: 'CHECK_WHATSAPP_READY' });
    return response;
  } catch (error) {
    return { ready: false, reason: 'Content script not loaded', error: error.message };
  }
};
```

## src/background/scheduler.js
```javascript
import { v4 as uuidv4 } from 'uuid';

// Active message queues
const messageQueues = {};

// Initialize the scheduler
export const initScheduler = async () => {
  // Load any persisted queue data from storage
  const data = await chrome.storage.local.get('messageQueues');
  
  if (data.messageQueues) {
    Object.entries(data.messageQueues).forEach(([queueId, queue]) => {
      if (queue.status === 'active') {
        // Resume processing of active queues
        messageQueues[queueId] = queue;
        processQueue(queueId);
      } else {
        // Just store paused queues
        messageQueues[queueId] = queue;
      }
    });
  }
  
  console.log('Message scheduler initialized');
};

// Schedule messages for sending
export const scheduleMessages = async (contacts, template, settings) => {
  // Generate a unique queue ID
  const queueId = uuidv4();
  
  // Create message queue
  const queue = {
    id: queueId,
    status: 'active',
    createdAt: new Date().toISOString(),
    contacts: contacts.map(contact => ({
      ...contact,
      status: 'pending',
      attempts: 0,
      sentAt: null,
      error: null
    })),
    template,
    settings: {
      delay: settings.delay || 3000,
      randomizeDelay: settings.randomizeDelay !== undefined ? settings.randomizeDelay : true,
      maxAttempts: settings.maxAttempts || 3,
      ...settings
    },
    stats: {
      total: contacts.length,
      pending: contacts.length,
      sent: 0,
      failed: 0
    }
  };
  
  // Save to memory
  messageQueues[queueId] = queue;
  
  // Persist to storage
  await persistQueueState();
  
  // Start processing
  processQueue(queueId);
  
  return { queueId };
};

// Process the message queue
const processQueue = async (queueId) => {
  const queue = messageQueues[queueId];
  
  if (!queue || queue.status !== 'active') {
    return;
  }
  
  // Find the next pending contact
  const nextContactIndex = queue.contacts.findIndex(c => c.status === 'pending');
  
  if (nextContactIndex === -1) {
    // No more pending contacts, mark queue as completed
    queue.status = 'completed';
    queue.completedAt = new Date().toISOString();
    await persistQueueState();
    return;
  }
  
  const contact = queue.contacts[nextContactIndex];
  contact.status = 'processing';
  await persistQueueState();
  
  try {
    // Find WhatsApp tab
    const tabs = await chrome.tabs.query({ url: 'https://web.whatsapp.com/*' });
    
    if (tabs.length === 0) {
      throw new Error('WhatsApp tab not found');
    }
    
    // Send message
    const response = await chrome.tabs.sendMessage(tabs[0].id, {
      type: 'SEND_WHATSAPP_MESSAGE',
      payload: {
        contact: contact.phone,
        message: processTemplate(queue.template, contact)
      }
    });
    
    if (response.success) {
      // Message sent successfully
      contact.status = 'sent';
      contact.sentAt = new Date().toISOString();
      queue.stats.sent++;
      queue.stats.pending--;
      
      // Update total messages sent
      const data = await chrome.storage.local.get('messagesSent');
      await chrome.storage.local.set({ 
        messagesSent: (data.messagesSent || 0) + 1 
      });
    } else {
      // Message failed
      contact.attempts++;
      
      if (contact.attempts >= queue.settings.maxAttempts) {
        contact.status = 'failed';
        contact.error = response.error || 'Max attempts reached';
        queue.stats.failed++;
        queue.stats.pending--;
      } else {
        contact.status = 'pending';
        contact.error = response.error || 'Unknown error';
      }
    }
  } catch (error) {
    // Error processing the message
    contact.attempts++;
    
    if (contact.attempts >= queue.settings.maxAttempts) {
      contact.status = 'failed';
      contact.error = error.message;
      queue.stats.failed++;
      queue.stats.pending--;
    } else {
      contact.status = 'pending';
      contact.error = error.message;
    }
  }
  
  // Save state
  await persistQueueState();
  
  // Calculate delay before next message
  let delay = queue.settings.delay;
  if (queue.settings.randomizeDelay) {
    // Add random variation of +/- 20%
    const variation = delay * 0.2;
    delay = delay + (Math.random() * variation * 2 - variation);
  }
  
  // Schedule next message
  setTimeout(() => {
    processQueue(queueId);
  }, delay);
};

// Process message template with contact data
const processTemplate = (template, contact) => {
  let processedMessage = template;
  
  // Replace variables with contact fields
  Object.entries(contact).forEach(([key, value]) => {
    const regex = new RegExp(`{{${key}}}`, 'g');
    processedMessage = processedMessage.replace(regex, value);
  });
  
  // Process spin text syntax - {option1|option2|option3}
  const spinTextRegex = /{([^{}]+)}/g;
  processedMessage = processedMessage.replace(spinTextRegex, (match, options) => {
    const choices = options.split('|');
    return choices[Math.floor(Math.random() * choices.length)];
  });
  
  return processedMessage;
};

// Get queue status
export const getQueueStatus = async (queueId) => {
  const queue = messageQueues[queueId];
  
  if (!queue) {
    throw new Error('Queue not found');
  }
  
  return {
    id: queue.id,
    status: queue.status,
    stats: queue.stats,
    createdAt: queue.createdAt,
    completedAt: queue.completedAt
  };
};

// Pause queue
export const pauseQueue = async (queueId) => {
  const queue = messageQueues[queueId];
  
  if (!queue) {
    throw new Error('Queue not found');
  }
  
  queue.status = 'paused';
  queue.pausedAt = new Date().toISOString();
  
  await persistQueueState();
  return true;
};

// Resume queue
export const resumeQueue = async (queueId) => {
  const queue = messageQueues[queueId];
  
  if (!queue) {
    throw new Error('Queue not found');
  }
  
  queue.status = 'active';
  queue.resumedAt = new Date().toISOString();
  
  await persistQueueState();
  processQueue(queueId);
  return true;
};

// Persist queue state to storage
const persistQueueState = async () => {
  await chrome.storage.local.set({ messageQueues });
};
```

## src/background/analytics.js
```javascript
// Initialize analytics tracking
export const initAnalytics = async () => {
  // Set up basic analytics tracking
  const setup = await chrome.storage.local.get('analyticsSetup');
  
  if (!setup.analyticsSetup) {
    await chrome.storage.local.set({
      analyticsSetup: true,
      analyticsData: {
        installDate: new Date().toISOString(),
        messagesSent: 0,
        messagesQueued: 0,
        activeSessions: 0,
        lastActive: new Date().toISOString(),
        features: {
          csvUpload: 0,
          aiAssistant: 0,
          scheduledMessages: 0
        }
      }
    });
  }
  
  // Set up event listeners for analytics
  chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
    if (message.type === 'TRACK_EVENT') {
      trackEvent(message.payload.event, message.payload.data);
      sendResponse({ success: true });
    }
  });
  
  console.log('Analytics initialized');
};

// Track usage events
const trackEvent = async (event, data = {}) => {
  const analyticsData = await chrome.storage.local.get('analyticsData');
  const updatedData = { ...analyticsData.analyticsData };
  
  // Update event-specific counters
  switch (event) {
    case 'MESSAGE_SENT':
      updatedData.messagesSent = (updatedData.messagesSent || 0) + 1;
      break;
    case 'MESSAGE_QUEUED':
      updatedData.messagesQueued = (updatedData.messagesQueued || 0) + 1;
      break;
    case 'FEATURE_USED':
      if (data.feature && updatedData.features[data.feature] !== undefined) {
        updatedData.features[data.feature] = (updatedData.features[data.feature] || 0) + 1;
      }
      break;
    case 'SESSION_STARTED':
      updatedData.activeSessions = (updatedData.activeSessions || 0) + 1;
      updatedData.lastActive = new Date().toISOString();
      break;
    case 'SESSION_ENDED':
      updatedData.activeSessions = Math.max(0, (updatedData.activeSessions || 0) - 1);
      updatedData.lastActive = new Date().toISOString();
      break;
  }
  
  // Save updated analytics data
  await chrome.storage.local.set({ analyticsData: updatedData });
  
  // Optional: Send to server if user has opted in
  const settings = await chrome.storage.local.get('settings');
  if (settings.settings?.analyticsOptIn) {
    // This would be implemented when backend is set up
    // sendToAnalyticsServer(event, data, updatedData);
  }
};
```

## src/content/index.js
```javascript
import { injectWhatsAppApi } from './injector';
import { sendMessage, checkWhatsAppReady } from './whatsapp-api';
import { setupResilienceMonitor } from './utils';

// Initialize content script
const init = async () => {
  console.log('WAZARD Pro content script initializing...');
  
  // Wait for WhatsApp to fully load
  await waitForWhatsApp();
  
  // Inject WhatsApp API helpers
  injectWhatsAppApi();
  
  // Setup message listeners
  setupMessageListeners();
  
  // Setup resilience monitoring (restore after page changes)
  setupResilienceMonitor();
  
  console.log('WAZARD Pro content script initialized');
};

// Wait for WhatsApp to fully load
const waitForWhatsApp = () => {
  return new Promise((resolve) => {
    const checkLoaded = () => {
      const appElement = document.querySelector('[data-testid="conversation-panel-wrapper"]');
      if (appElement) {
        resolve();
      } else {
        setTimeout(checkLoaded, 1000);
      }
    };
    
    checkLoaded();
  });
};

// Setup message listeners for communication with background script
const setupMessageListeners = () => {
  chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
    console.log('Content script received message:', message.type);
    
    switch (message.type) {
      case 'SEND_WHATSAPP_MESSAGE':
        sendMessage(message.payload.contact, message.payload.message)
          .then(result => sendResponse({ success: true, result }))
          .catch(error => sendResponse({ success: false, error: error.message }));
        return true;
        
      case 'CHECK_WHATSAPP_READY':
        checkWhatsAppReady()
          .then(status => sendResponse(status))
          .catch(error => sendResponse({ ready: false, error: error.message }));
        return true;
        
      default:
        sendResponse({ success: false, error: 'Unknown message type' });
        return false;
    }
  });
};

// Initialize the content script
init();
```

## src/content/injector.js
```javascript
// Inject WhatsApp API interaction code
export const injectWhatsAppApi = () => {
  // Create script element to inject
  const script = document.createElement('script');
  script.textContent = `
    window.WAZARD = {
      ready: false,
      
      // Initialize WAZARD API
      init() {
        if (this.ready) return;
        
        // Store the original WebSocket send method
        if (!window.originalWsSend && window.WebSocket) {
          const originalWsSend = window.WebSocket.prototype.send;
          window.originalWsSend = originalWsSend;
          
          // Override WebSocket send to intercept messages
          window.WebSocket.prototype.send = function(data) {
            // Call original method
            originalWsSend.call(this, data);
            
            // Process message data if needed
            try {
              if (typeof data === 'string') {
                const parsedData = JSON.parse(data);
                if (parsedData && parsedData.messageId) {
                  // Notify about message being sent
                  window.dispatchEvent(new CustomEvent('wazard_message_sent', {
                    detail: { messageId: parsedData.messageId }
                  }));
                }
              }
            } catch (e) {
              // Not a JSON string, ignore
            }
          };
        }
        
        this.ready = true;
        window.dispatchEvent(new CustomEvent('wazard_init'));
      },
      
      // Send message to a contact
      async sendMessage(phone, message) {
        try {
          // Ensure phone is in the right format
          phone = phone.replace(/[^0-9]/g, '');
          
          // Check if WhatsApp Store is available
          const store = window.Store;
          if (!store) throw new Error('WhatsApp store not available');
          
          // Open chat with contact
          const chat = await store.Chat.find(store.WidFactory.createWid(phone + '@c.us'));
          if (!chat) throw new Error('Could not find chat for contact');
          
          // Compose and send message
          const msg = {
            body: message,
            type: 'chat',
            t: Date.now()
          };
          
          // Send using WhatsApp's internal APIs
          await store.SendMessage.addAndSendMsgToChat(chat, msg);
          
          return { success: true };
        } catch (error) {
          console.error('Error sending message:', error);
          return { success: false, error: error.message };
        }
      },
      
      // Check if WhatsApp web is ready
      isReady() {
        // Verify if WhatsApp Store is available
        if (!window.Store || !window.Store.Chat) {
          return { ready: false, reason: 'WhatsApp web is not fully loaded' };
        }
        
        // Check if user is logged in
        if (!window.Store.Conn || !window.Store.Conn.isLoggedIn()) {
          return { ready: false, reason: 'User is not logged in to WhatsApp' };
        }
        
        return { ready: true };
      }
    };
    
    // Initialize WAZARD API
    window.WAZARD.init();
  `;
  
  // Append script to page
  document.head.appendChild(script);
  script.remove();
  
  console.log('WhatsApp API injected');
};
```

## src/content/whatsapp-api.js
```javascript
// Send WhatsApp message using injected API
export const sendMessage = async (phone, message) => {
  return new Promise((resolve, reject) => {
    // Create a unique ID for this request
    const requestId = 'req_' + Date.now();
    
    // Create and dispatch custom event
    const event = new CustomEvent('wazard_send_message', {
      detail: { requestId, phone, message }
    });
    
    // Setup listener for response
    const responseListener = (e) => {
      if (e.detail.requestId === requestId) {
        window.removeEventListener('wazard_send_message_response', responseListener);
        
        if (e.detail.success) {
          resolve(e.detail.result);
        } else {
          reject(new Error(e.detail.error));
        }
      }
    };
    
    // Listen for response
    window.addEventListener('wazard_send_message_response', responseListener);
    
    // Set timeout for response
    setTimeout(() => {
      window.removeEventListener('wazard_send_message_response', responseListener);
      reject(new Error('Request timed out'));
    }, 30000);
    
    // Dispatch request
    window.dispatchEvent(event);
    
    // Execute script to handle the request
    executeInPageContext(`
      (function() {
        const detail = ${JSON.stringify({ requestId, phone, message })};
        
        // Send message using WAZARD API
        window.WAZARD.sendMessage(detail.phone, detail.message)
          .then(result => {
            window.dispatchEvent(new CustomEvent('wazard_send_message_response', {
              detail: { requestId: detail.requestId, success: true, result }
            }));
          })
          .catch(error => {
            window.dispatchEvent(new CustomEvent('wazard_send_message_response', {
              detail: { requestId: detail.requestId, success: false, error: error.message }
            }));
          });
      })();
    `);
  });
};

// Check if WhatsApp is ready
export const checkWhatsAppReady = async () => {
  return new Promise((resolve, reject) => {
    // Create a unique ID for this request
    const requestId = 'req_' + Date.now();
    
    // Setup listener for response
    const responseListener = (e) => {
      if (e.detail.requestId === requestId) {
        window.removeEventListener('wazard_check_ready_response', responseListener);
        resolve(e.detail.status);
      }
    };
    
    // Listen for response
    window.addEventListener('wazard_check_ready_response', responseListener);
    
    // Set timeout for response
    setTimeout(() => {
      window.removeEventListener('wazard_check_ready_response', responseListener);
      reject(new Error('Request timed out'));
    }, 5000);
    
    // Execute script to check if WhatsApp is ready
    executeInPageContext(`
      (function() {
        const requestId = '${requestId}';
        const status = window.WAZARD ? window.WAZARD.isReady() : { ready: false, reason: 'WAZARD not initialized' };
        
        window.dispatchEvent(new CustomEvent('w
