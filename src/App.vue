<template>
  <div class="container">
    <h1>Internet Connection Status Checker</h1>
    <p><strong>Connection Status:</strong><span>{{ status }}</span></p>
    <p><strong>IP Address:</strong><span>{{ ipAddress }}</span></p>
    <p><strong>Network Strength:</strong><span>{{ networkStrength }}</span></p>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const status = ref('Checking...');
    const ipAddress = ref('-');
    const networkStrength = ref('-');

    const checkInternetConnection = () => {
      status.value = 'Checking...';
      if (navigator.onLine) {
        fetch('https://api.ipify.org/?format=json')
          .then((response) => response.json())
          .then((data) => {
            ipAddress.value = data.ip;
            status.value = 'Connected';

            const connection = navigator.connection;
            const strength = connection ? `${connection.downlink} Mbps` : 'Unknown';
            networkStrength.value = strength;
          })
          .catch(() => {
            status.value = 'Disconnected';
            ipAddress.value = '-';
            networkStrength.value = '-';
          });
      } else {
        status.value = 'Disconnected';
        ipAddress.value = '-';
        networkStrength.value = '-';
      }
    };

    onMounted(() => {
      checkInternetConnection();
      window.addEventListener('online', checkInternetConnection);
      window.addEventListener('offline', checkInternetConnection);
    });

    return {
      status,
      ipAddress,
      networkStrength,
    };
  },
};
</script>

<style scoped>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: linear-gradient(to right, #f0f4f8, #4c8bf5);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  color: #333;
}

.container {
  background-color: #ffffff;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  max-width: 400px;
  width: 100%;
  text-align: center;
}

h1 {
  font-size: 1.8rem;
  margin-bottom: 20px;
  color: #333;
}

p {
  font-size: 1rem;
  margin: 10px 0;
  color: #555;
}

strong {
  color: #444;
}

span {
  font-weight: bold;
  font-size: 1.1rem;
  color: #2d6fbb;
}

p > span {
  display: inline-block;
  padding: 0.3em 1em;
  background-color: #f0f0f0;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

@media (max-width: 600px) {
  .container {
    padding: 20px;
  }

  h1 {
    font-size: 1.5rem;
  }

  p {
    font-size: 0.9rem;
  }
}
</style>
