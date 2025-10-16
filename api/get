// api/get-logins.js
import { createClient } from '@vercel/kv';

const kv = createClient({
  url: process.env.KV_REST_API_URL,
  token: process.env.KV_REST_API_TOKEN,
});

const ADMIN_PASSWORD = 'admin123'; // Change this!

export default async function handler(req, res) {
  // Enable CORS
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');

  if (req.method === 'OPTIONS') {
    return res.status(200).end();
  }

  if (req.method !== 'GET' && req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    // Check admin password
    const password = req.headers.authorization || req.query.password || req.body?.password;

    if (password !== ADMIN_PASSWORD) {
      return res.status(401).json({ error: 'Unauthorized' });
    }

    // Get all logins
    const logins = await kv.get('logins') || [];

    return res.status(200).json({
      success: true,
      count: logins.length,
      logins: logins.reverse() // Show newest first
    });

  } catch (error) {
    console.error('Error getting logins:', error);
    return res.status(500).json({ 
      error: 'Failed to get logins',
      details: error.message 
    });
  }
}