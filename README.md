<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crisis Coach - Your Wingman for Life</title>
    <!-- Keep using CDN React for now -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- PWA Features -->
    <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiQ3Jpc2lzIENvYWNoIiwic2hvcnRfbmFtZSI6IkNvYWNoIiwic3RhcnRfdXJsIjoiLyIsImRpc3BsYXkiOiJzdGFuZGFsb25lIiwidGhlbWVfY29sb3IiOiIjMzB4NGY3IiwiYmFja2dyb3VuZF9jb2xvciI6IiNmZmZmZmYifQ==">
    <meta name="theme-color" content="#3b82f7">
    
    <style>
        /* Enhanced Styling */
        :root {
            --primary-blue: #3b82f7;
            --primary-purple: #8b5cf6;
            --danger-red: #ef4444;
            --warning-yellow: #f59e0b;
            --success-green: #10b981;
            --neutral-gray: #6b7280;
        }

        body { 
            margin: 0; 
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #f9fafb;
            overflow-x: hidden;
        }

        /* Custom scrollbar */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 2px; }

        /* Advanced Animations */
        .message-enter {
            animation: slideInUp 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        @keyframes slideInUp {
            from { 
                opacity: 0; 
                transform: translateY(24px) scale(0.95); 
            }
            to { 
                opacity: 1; 
                transform: translateY(0) scale(1); 
            }
        }

        .typing-indicator {
            animation: pulse 1.8s ease-in-out infinite;
        }

        .typing-dots div {
            animation: typingBounce 1.4s infinite;
        }

        .typing-dots div:nth-child(2) { animation-delay: 0.2s; }
        .typing-dots div:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typingBounce {
            0%, 60%, 100% { transform: translateY(0); }
            30% { transform: translateY(-8px); }
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }

        /* Risk Level Indicators */
        .risk-high { 
            background: linear-gradient(135deg, #fee2e2, #fecaca);
            border: 2px solid var(--danger-red);
            animation: urgentPulse 2s infinite;
        }

        .risk-moderate { 
            background: linear-gradient(135deg, #fef3c7, #fde68a);
            border: 2px solid var(--warning-yellow);
        }

        .risk-normal { 
            background: linear-gradient(135deg, #ecfdf5, #d1fae5);
            border: 2px solid var(--success-green);
        }

        @keyframes urgentPulse {
            0%, 100% { 
                box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.4);
            }
            50% { 
                box-shadow: 0 0 0 8px rgba(239, 68, 68, 0);
            }
        }

        /* Enhanced Button Styles */
        .btn-primary {
            background: linear-gradient(135deg, var(--primary-blue), var(--primary-purple));
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .btn-primary:hover {
            transform: translateY(-1px);
            box-shadow: 0 8px 25px rgba(59, 130, 247, 0.3);
        }

        .btn-primary:active {
            transform: translateY(0);
        }

        /* Network Status Indicator */
        .network-status {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1000;
            padding: 8px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
            transition: all 0.3s ease;
        }

        .network-online {
            background: #10b981;
            color: white;
        }

        .network-offline {
            background: #ef4444;
            color: white;
            animation: shake 0.5s ease-in-out;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-4px); }
            75% { transform: translateX(4px); }
        }

        /* Message Bubble Enhancements */
        .message-bubble {
            position: relative;
            backdrop-filter: blur(10px);
            transition: all 0.2s ease;
        }

        .message-bubble:hover {
            transform: scale(1.02);
        }

        .message-urgent::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, #ef4444, #dc2626);
            border-radius: inherit;
            z-index: -1;
            animation: urgentGlow 1.5s ease-in-out infinite alternate;
        }

        @keyframes urgentGlow {
            0% { opacity: 0.5; }
            100% { opacity: 0.8; }
        }

        /* Loading States */
        .skeleton {
            background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
            background-size: 200% 100%;
            animation: loading 1.5s infinite;
        }

        @keyframes loading {
            0% { background-position: 200% 0; }
            100% { background-position: -200% 0; }
        }
    </style>
</head>
<body>
    <div id="root"></div>
    
    <!-- Network Status Indicator -->
    <div id="network-status" class="network-status network-online">
        🟢 Online
    </div>

    <script type="text/babel">
        // ============================================
        // STATE MANAGEMENT (Organized like Zustand)
        // ============================================
        
        const ChatState = {
            // Initial state
            state: {
                messages: [
                    {
                        id: 1,
                        type: 'bot',
                        content: "Yo! What's good, bro? I'm Coach - think of me as your wingman for life's tough stuff. No judgment here, just real talk when you need it. How you doing today, man?",
                        timestamp: new Date(Date.now() - 300000),
                        urgent: false
                    }
                ],
                isTyping: false,
                riskLevel: 'normal',
                isOnline: navigator.onLine,
                conversationContext: {
                    messageCount: 0,
                    sessionStart: new Date(),
                    riskHistory: [],
                    lastActivity: new Date()
                }
            },

            // Subscribers for state changes
            subscribers: new Set(),

            // Subscribe to state changes
            subscribe(callback) {
                this.subscribers.add(callback);
                return () => this.subscribers.delete(callback);
            },

            // Notify all subscribers
            notify() {
                this.subscribers.forEach(callback => callback(this.state));
            },

            // Update state
            setState(updates) {
                this.state = { ...this.state, ...updates };
                this.notify();
            },

            // Actions
            actions: {
                addMessage: (message) => {
                    const newMessage = {
                        ...message,
                        id: Date.now() + Math.random(),
                        timestamp: new Date()
                    };
                    
                    ChatState.setState({
                        messages: [...ChatState.state.messages, newMessage],
                        conversationContext: {
                            ...ChatState.state.conversationContext,
                            messageCount: ChatState.state.conversationContext.messageCount + 1,
                            lastActivity: new Date()
                        }
                    });
                },

                setTyping: (isTyping) => {
                    ChatState.setState({ isTyping });
                },

                setRiskLevel: (level) => {
                    ChatState.setState({ 
                        riskLevel: level,
                        conversationContext: {
                            ...ChatState.state.conversationContext,
                            riskHistory: [
                                ...ChatState.state.conversationContext.riskHistory,
                                { level, timestamp: new Date() }
                            ]
                        }
                    });
                },

                setOnlineStatus: (isOnline) => {
                    ChatState.setState({ isOnline });
                    
                    // Update network status indicator
                    const indicator = document.getElementById('network-status');
                    if (indicator) {
                        indicator.className = `network-status ${isOnline ? 'network-online' : 'network-offline'}`;
                        indicator.textContent = isOnline ? '🟢 Online' : '🔴 Offline';
                    }
                }
            }
        };

        // ============================================
        // UTILITIES & HELPERS
        // ============================================
        
        const Utils = {
            // Format time
            formatTime: (timestamp) => {
                return new Date(timestamp).toLocaleTimeString([], { 
                    hour: '2-digit', 
                    minute: '2-digit' 
                });
            },

            // Analyze message risk
            analyzeMessage: (content) => {
                const keywords = {
                    high: ['suicide', 'kill myself', 'end it all', 'no point', 'worthless', 'hopeless', 'can\'t go on', 'want to die', 'better off dead'],
                    moderate: ['depressed', 'anxious', 'overwhelmed', 'struggling', 'difficult', 'hard time', 'stressed', 'burnt out', 'exhausted', 'lonely'],
                    positive: ['better', 'good', 'improving', 'happy', 'grateful', 'hopeful', 'progress', 'great', 'awesome', 'solid']
                };

                const lowerContent = content.toLowerCase();
                
                if (keywords.high.some(keyword => lowerContent.includes(keyword))) {
                    return 'high';
                }
                if (keywords.moderate.some(keyword => lowerContent.includes(keyword))) {
                    return 'moderate';
                }
                if (keywords.positive.some(keyword => lowerContent.includes(keyword))) {
                    return 'positive';
                }
                return 'normal';
            },

            // Generate bot response
            generateResponse: (riskLevel) => {
                const responses = {
                    high: [
                        "Yo man, I'm not gonna lie - what you just said has me worried about you, for real. You're my guy and I need to make sure you're safe. Can we get you talking to someone who knows their stuff right now?",
                        "Bro, that's some heavy stuff you're carrying. You don't gotta face this alone though - that's what we're here for. Let me hook you up with someone who can help you work through this, yeah?",
                        "Hold up man, I'm hearing some really serious stuff from you. Your life matters, bro. Let's get you connected with someone right now who can help. No shame in getting backup when you need it."
                    ],
                    moderate: [
                        "Damn man, sounds like you're going through it right now. That takes some serious guts to open up like that. What's been the hardest part about all this?",
                        "I hear you, bro. Life's been throwing some punches lately, huh? You're stronger than you think, but even strong dudes need backup sometimes. Who's in your corner right now?",
                        "Real talk - that sounds rough as hell. But you trusted me with this, and that means something. What usually helps when you're feeling like this? Let's figure this out together.",
                        "Man, I appreciate you being straight with me about this. Takes courage to admit when things are tough. How long have you been dealing with this?"
                    ],
                    positive: [
                        "Yooo, that's what I'm talking about! Love hearing you sound good, man. What's got you feeling solid today?",
                        "Now that's the energy I like to see! Good to hear you're having a better day, bro. What's different about today?",
                        "Yo, that's awesome to hear! It's good to celebrate the wins, you know? What's been working for you lately?"
                    ],
                    normal: [
                        "I appreciate you keeping it real with me, man. How's your sleep been? You know that stuff matters more than people think.",
                        "Thanks for checking in, bro. What's been on your mind lately? Sometimes just talking it out helps.",
                        "How you taking care of yourself today, man? You doing anything good for you?",
                        "What's good, bro? Anything interesting happening in your world today?"
                    ]
                };

                const responseArray = responses[riskLevel] || responses.normal;
                return responseArray[Math.floor(Math.random() * responseArray.length)];
            },

            // Calculate typing delay based on content length
            getTypingDelay: (content) => {
                return Math.min(4000, Math.max(1200, content.length * 60));
            },

            // Scroll to bottom smoothly
            scrollToBottom: () => {
                const messagesContainer = document.querySelector('.messages-container');
                if (messagesContainer) {
                    messagesContainer.scrollTo({
                        top: messagesContainer.scrollHeight,
                        behavior: 'smooth'
                    });
                }
            }
        };

        // ============================================
        // ENHANCED COMPONENTS
        // ============================================
        
        const { useState, useEffect, useRef, useCallback } = React;

        // Message Component with enhanced styling
        const Message = ({ message, index }) => {
            const isBot = message.type === 'bot';
            const isUrgent = message.urgent;
            
            return (
                <div 
                    className={`flex ${isBot ? 'justify-start' : 'justify-end'} mb-4 message-enter`}
                    style={{ animationDelay: `${index * 50}ms` }}
                >
                    <div className={`flex items-start space-x-2 max-w-xs lg:max-w-md ${
                        isBot ? 'flex-row' : 'flex-row-reverse space-x-reverse'
                    }`}>
                        {/* Enhanced Avatar */}
                        <div className={`w-8 h-8 rounded-full flex items-center justify-center flex-shrink-0 shadow-sm ${
                            isBot 
                                ? isUrgent 
                                    ? 'bg-red-500 text-white' 
                                    : 'bg-gradient-to-br from-blue-500 to-purple-600 text-white'
                                : 'bg-gradient-to-br from-gray-400 to-gray-500 text-white'
                        }`}>
                            {isBot ? (
                                isUrgent ? '🚨' : '🤖'
                            ) : (
                                '👤'
                            )}
                        </div>

                        {/* Enhanced Message Bubble */}
                        <div className={`message-bubble rounded-2xl px-4 py-3 shadow-lg max-w-xs relative ${
                            isBot 
                                ? isUrgent 
                                    ? 'message-urgent bg-red-50 border-2 border-red-300 text-red-900' 
                                    : 'bg-white text-gray-800 border border-gray-200'
                                : 'bg-gradient-to-br from-blue-500 to-blue-600 text-white shadow-blue-200'
                        }`}>
                            <p className="text-sm whitespace-pre-line leading-relaxed">
                                {message.content}
                            </p>
                            
                            <p className={`text-xs mt-2 ${
                                isBot 
                                    ? isUrgent 
                                        ? 'text-red-600' 
                                        : 'text-gray-500'
                                    : 'text-blue-100'
                            }`}>
                                {Utils.formatTime(message.timestamp)}
                            </p>

                            {/* Urgent pulse indicator */}
                            {isUrgent && (
                                <div className="absolute -top-1 -right-1 w-3 h-3 bg-red-500 rounded-full animate-pulse" />
                            )}
                        </div>
                    </div>
                </div>
            );
        };

        // Enhanced Typing Indicator
        const TypingIndicator = () => (
            <div className="flex justify-start mb-4 message-enter">
                <div className="flex items-start space-x-2">
                    <div className="w-8 h-8 rounded-full bg-gradient-to-br from-blue-500 to-purple-600 flex items-center justify-center text-white shadow-sm">
                        🤖
                    </div>
                    <div className="bg-white rounded-2xl px-4 py-3 shadow-lg border border-gray-200 typing-indicator">
                        <div className="flex space-x-1 items-center">
                            <span className="text-sm text-gray-500 mr-3">Coach is thinking...</span>
                            <div className="typing-dots flex space-x-1">
                                <div className="w-2 h-2 bg-blue-500 rounded-full"></div>
                                <div className="w-2 h-2 bg-blue-500 rounded-full"></div>
                                <div className="w-2 h-2 bg-blue-500 rounded-full"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        );

        // Main App Component
        const CrisisCoachApp = () => {
            const [localState, setLocalState] = useState(ChatState.state);
            const [newMessage, setNewMessage] = useState('');
            const messagesEndRef = useRef(null);

            // Subscribe to state changes
            useEffect(() => {
                const unsubscribe = ChatState.subscribe(setLocalState);
                return unsubscribe;
            }, []);

            // Network status monitoring
            useEffect(() => {
                const handleOnline = () => ChatState.actions.setOnlineStatus(true);
                const handleOffline = () => ChatState.actions.setOnlineStatus(false);

                window.addEventListener('online', handleOnline);
                window.addEventListener('offline', handleOffline);

                return () => {
                    window.removeEventListener('online', handleOnline);
                    window.removeEventListener('offline', handleOffline);
                };
            }, []);

            // Auto-scroll to bottom
            useEffect(() => {
                setTimeout(Utils.scrollToBottom, 100);
            }, [localState.messages, localState.isTyping]);

            // Enhanced message sending
            const handleSendMessage = useCallback(() => {
                if (!newMessage.trim() || localState.isTyping || !localState.isOnline) return;

                const userMessage = {
                    type: 'user',
                    content: newMessage.trim()
                };

                ChatState.actions.addMessage(userMessage);
                
                const risk = Utils.analyzeMessage(newMessage);
                ChatState.actions.setRiskLevel(risk);
                ChatState.actions.setTyping(true);

                const currentMessage = newMessage.trim();
                setNewMessage('');

                const typingDelay = Utils.getTypingDelay(currentMessage);

                setTimeout(() => {
                    if (risk === 'high') {
                        ChatState.actions.addMessage({
                            type: 'bot',
                            content: "🚨 Listen up bro - I got your back and so do these guys:\n\n📞 Call 988 - These people know what they're doing, 24/7\n💬 Text HOME to 741741 - Quick and easy, no talking needed\n🚨 Call 911 if things are getting real scary\n\nYou matter, man. Seriously. Want me to help you connect with one of these? No shame in getting backup when the game gets tough.",
                            urgent: true
                        });
                    } else {
                        ChatState.actions.addMessage({
                            type: 'bot',
                            content: Utils.generateResponse(risk)
                        });

                        // Follow-up for moderate risk
                        if (risk === 'moderate') {
                            setTimeout(() => {
                                ChatState.actions.addMessage({
                                    type: 'bot',
                                    content: "Real talk though - you got people you can hit up today? Brothers, family, anyone? I'm here for you but sometimes you need someone you can grab a beer with, you know? I can also hook you up with some pros if that's more your speed."
                                });
                            }, 4000);
                        }
                    }
                    ChatState.actions.setTyping(false);
                }, typingDelay);
            }, [newMessage, localState.isTyping, localState.isOnline]);

            // Emergency resources
            const addUrgentResources = useCallback(() => {
                ChatState.actions.addMessage({
                    type: 'bot',
                    content: "🚨 Listen up bro - I got your back and so do these guys:\n\n📞 Call 988 - These people know what they're doing, 24/7\n💬 Text HOME to 741741 - Quick and easy, no talking needed\n🚨 Call 911 if things are getting real scary\n\nYou matter, man. Seriously. Want me to help you connect with one of these? No shame in getting backup when the game gets tough.",
                    urgent: true
                });
            }, []);

            // Quick responses
            const quickResponses = [
                { text: "I'm feeling swamped today", style: "bg-yellow-100 text-yellow-800 hover:bg-yellow-200 border-yellow-300" },
                { text: "Having a solid day", style: "bg-green-100 text-green-800 hover:bg-green-200 border-green-300" },
                { text: "Need to vent about something", style: "bg-blue-100 text-blue-800 hover:bg-blue-200 border-blue-300" },
                { text: "Going through it right now", style: "bg-red-100 text-red-800 hover:bg-red-200 border-red-300" }
            ];

            const getRiskConfig = (level) => {
                switch (level) {
                    case 'high':
                        return { color: 'bg-red-400', message: '🚨 Crisis support active', class: 'risk-high' };
                    case 'moderate':
                        return { color: 'bg-yellow-400', message: '⚠️ Elevated concern', class: 'risk-moderate' };
                    default:
                        return { color: 'bg-green-400', message: '✅ Monitoring active', class: 'risk-normal' };
                }
            };

            const riskConfig = getRiskConfig(localState.riskLevel);

            return (
                <div className="max-w-md mx-auto bg-white h-screen flex flex-col shadow-2xl relative overflow-hidden">
                    {/* Enhanced Header */}
                    <div className="bg-gradient-to-r from-blue-600 via-purple-600 to-blue-800 text-white p-4 relative overflow-hidden">
                        {/* Background Pattern */}
                        <div className="absolute inset-0 opacity-10">
                            <div className="absolute top-0 left-0 w-20 h-20 bg-white rounded-full -translate-x-10 -translate-y-10"></div>
                            <div className="absolute bottom-0 right-0 w-32 h-32 bg-white rounded-full translate-x-16 translate-y-16"></div>
                        </div>

                        <div className="relative z-10 flex items-center justify-between">
                            <div className="flex items-center space-x-3">
                                <div className="w-12 h-12 bg-white bg-opacity-20 rounded-full flex items-center justify-center backdrop-blur-sm border border-white border-opacity-30">
                                    🛡️
                                </div>
                                <div>
                                    <h1 className="font-bold text-xl">Coach</h1>
                                    <div className="flex items-center space-x-2">
                                        <div className={`w-2 h-2 rounded-full ${riskConfig.color} shadow-sm`}></div>
                                        <p className="text-sm text-blue-100 font-medium">
                                            {localState.isOnline ? 'Your wingman for life' : 'Reconnecting...'}
                                        </p>
                                    </div>
                                </div>
                            </div>

                            <div className="flex items-center space-x-2">
                                <div className={`px-3 py-1 rounded-full text-xs font-medium ${riskConfig.class}`}>
                                    {riskConfig.message}
                                </div>
                            </div>
                        </div>
                    </div>

                    {/* Crisis Alert Banner */}
                    {localState.riskLevel === 'high' && (
                        <div className="bg-gradient-to-r from-red-500 to-red-600 text-white p-3 text-center relative overflow-hidden">
                            <div className="absolute inset-0 bg-red-600 opacity-50 animate-pulse"></div>
                            <div className="relative z-10">
                                <div className="flex items-center justify-center space-x-2">
                                    <span className="font-bold">🚨 Bro, I'm here for you</span>
                                </div>
                                <p className="text-sm mt-1 opacity-90">You don't gotta go through this alone, man.</p>
                            </div>
                        </div>
                    )}

                    {/* Enhanced Messages Container */}
                    <div className="flex-1 overflow-y-auto p-4 bg-gradient-to-b from-gray-50 to-white messages-container">
                        {localState.messages.map((message, index) => (
                            <Message key={message.id} message={message} index={index} />
                        ))}
                        
                        {localState.isTyping && <TypingIndicator />}
                        <div ref={messagesEndRef} />
                    </div>

                    {/* Enhanced Quick Actions */}
                    <div className="flex flex-wrap gap-2 p-4 bg-gray-50 border-t border-gray-100">
                        {quickResponses.map((response, index) => (
                            <button
                                key={index}
                                onClick={() => setNewMessage(response.text)}
                                disabled={localState.isTyping || !localState.isOnline}
                                className={`px-3 py-2 rounded-full text-sm font-medium transition-all duration-200 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed border shadow-sm ${response.style}`}
                            >
                                {response.text}
                            </button>
                        ))}
                    </div>

                    {/* Enhanced Input Area */}
                    <div className="border-t bg-white p-4 relative">
                        <div className="flex space-x-3">
                            <div className="flex-1 relative">
                                <textarea
                                    value={newMessage}
                                    onChange={(e) => setNewMessage(e.target.value)}
                                    onKeyPress={(e) => {
                                        if (e.key === 'Enter' && !e.shiftKey) {
                                            e.preventDefault();
                                            handleSendMessage();
                                        }
                                    }}
                                    placeholder={
                                        localState.isTyping 
                                            ? "Coach is responding..." 
                                            : !localState.isOnline 
                                                ? "Reconnecting..." 
                                                : "What's going on, man?"
                                    }
                                    disabled={localState.isTyping || !localState.isOnline}
                                    maxLength={500}
                                    rows={1}
                                    className="w-full border-2 border-gray-200 rounded-full px-4 py-2 focus:outline-none focus:border-blue-500 focus:ring-4 focus:ring-blue-100 transition-all duration-200 resize-none disabled:bg-gray-100 disabled:cursor-not-allowed shadow-sm"
                                    style={{
                                        minHeight: '44px',
                                        maxHeight: '120px'
                                    }}
                                    onInput={(e) => {
                                        e.target.style.height = 'auto';
                                        e.target.style.height = Math.min(e.target.scrollHeight, 120) + 'px';
                                    }}
                                />
                                
                                {/* Character Counter */}
                                {newMessage.length > 400 && (
                                    <div className={`absolute -bottom-5 right-2 text-xs ${
                                        500 - newMessage.length <= 50 ? 'text-red-500' : 'text-gray-500'
                                    
