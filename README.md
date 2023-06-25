import React, { useState } from 'react';
import io from 'socket.io-client';

const user_list = ["Alan", "Bob", "Charlie", "Dave"]; // Add more users if needed

const ChatApp = () => {
  const [message, setMessage] = useState('');
  const [chatMessages, setChatMessages] = useState([]);
  const [likes, setLikes] = useState({});

  const socket = io('http://localhost:3001'); // Replace with your server URL

  const handleInputChange = (event) => {
    setMessage(event.target.value);
  };

  const handleSendMessage = () => {
    if (message.trim() !== '') {
      const username = user_list[Math.floor(Math.random() * user_list.length)];
      socket.emit('message', { username, message, likes: 0 });
      setMessage('');
    }
  };

  const handleLike = (index) => {
    const updatedLikes = { ...likes };
    updatedLikes[index] = (updatedLikes[index] || 0) + 1;
    setLikes(updatedLikes);
  };

  socket.on('message', (message) => {
    setChatMessages((prevMessages) => [...prevMessages, message]);
  });

  return (
    <div>
      <div>
        <input type="text" value={message} onChange={handleInputChange} />
        <button onClick={handleSendMessage}>Send</button>
      </div>
      <div>
        {chatMessages.map((chat, index) => (
          <div key={index}>
            <span>{chat.username}: {chat.message}</span>
            <button onClick={() => handleLike(index)}>
              Like ({likes[index] || 0})
            </button>
          </div>
        ))}
      </div>
    </div>
  );
};

export default ChatApp;
