// Netlify Function to securely proxy the request to the Gemini API
// This function needs to be installed in netlify/functions/generate.js

const fetch = require('node-fetch');

// The Gemini API key will be automatically loaded from Netlify Environment Variables
// You MUST set an environment variable named GEMINI_API_KEY in your Netlify settings.
const GEMINI_API_KEY = process.env.GEMINI_API_KEY; 

const GEMINI_API_URL = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent";

exports.handler = async (event, context) => {
   // Only allow POST requests
   if (event.httpMethod !== 'POST') {
       return { statusCode: 405, body: 'Method Not Allowed' };
   }

   // Check for API Key
   if (!GEMINI_API_KEY) {
       console.error("GEMINI_API_KEY environment variable is not set.");
       return {
           statusCode: 500,
           body: JSON.stringify({ error: "Server misconfigured: API Key missing." }),
       };
   }

   try {
       // Parse the payload sent from the frontend (index.html)
       const requestPayload = JSON.parse(event.body);

       // Call the Gemini API securely from the backend
       const response = await fetch(`${GEMINI_API_URL}?key=${GEMINI_API_KEY}`, {
           method: 'POST',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify(requestPayload)
       });

       // Handle non-200 responses from the Gemini API
       if (!response.ok) {
           const errorText = await response.text();
           console.error('Gemini API Error:', errorText);
           return {
               statusCode: response.status,
               body: JSON.stringify({ error: 'Gemini API call failed', details: errorText }),
           };
       }

       const data = await response.json();

       // Return the raw response from the Gemini API back to the client
       return {
           statusCode: 200,
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify(data),
       };

   } catch (error) {
       console.error('Netlify Function Error:', error);
       return {
           statusCode: 500,
           body: JSON.stringify({ error: 'Internal Server Error', message: error.message }),
       };
   }
};