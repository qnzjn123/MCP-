<template>
  <div class="network-monitor">
    <div class="panel-header">
      <h2>네트워크 활동</h2>
      <div class="time-selector">
        <button class="time-btn" @click="changeTimeFrame('1h')" :class="{ active: timeFrame === '1h' }">1시간</button>
        <button class="time-btn" @click="changeTimeFrame('6h')" :class="{ active: timeFrame === '6h' }">6시간</button>
        <button class="time-btn" @click="changeTimeFrame('24h')" :class="{ active: timeFrame === '24h' }">24시간</button>
      </div>
    </div>
    
    <div class="chart-container">
      <LineChart
        :chart-data="chartData"
        :chart-options="chartOptions"
      />
    </div>
    
    <div class="network-stats">
      <div class="stat-box">
        <div class="stat-label">총 트래픽</div>
        <div class="stat-value">{{ formatTraffic(totalTraffic) }}</div>
      </div>
      <div class="stat-box">
        <div class="stat-label">초당 요청</div>
        <div class="stat-value">{{ requestsPerSecond.toFixed(2) }}</div>
      </div>
      <div class="stat-box">
        <div class="stat-label">비정상 연결</div>
        <div class="stat-value">{{ abnormalConnections }}</div>
      </div>
      <div class="stat-box">
        <div class="stat-label">차단된 IP</div>
        <div class="stat-value">{{ blockedIPs }}</div>
      </div>
    </div>
    
    <div class="geo-traffic">
      <h3>지역별 트래픽</h3>
      <div class="geo-list">
        <div v-for="(item, index) in geoTraffic" :key="index" class="geo-item">
          <div class="geo-flag">{{ item.flag }}</div>
          <div class="geo-name">{{ item.country }}</div>
          <div class="geo-bar-container">
            <div class="geo-bar" :style="{ width: `${item.percentage}%` }"></div>
          </div>
          <div class="geo-percentage">{{ item.percentage }}%</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import { LineChart } from 'vue-chart-3';
import { Chart, registerables } from 'chart.js';

Chart.register(...registerables);

export default {
  name: 'NetworkMonitor',
  components: {
    LineChart
  },
  props: {
    networkData: {
      type: Object,
      default: () => ({
        traffic: [],
        geoTraffic: [],
        stats: {
          totalTraffic: 0,
          requestsPerSecond: 0,
          abnormalConnections: 0,
          blockedIPs: 0
        }
      })
    }
  },
  setup(props) {
    const timeFrame = ref('1h');
    
    function changeTimeFrame(frame) {
      timeFrame.value = frame;
    }
    
    const filteredData = computed(() => {
      // 여기서는 더미 데이터를 반환하지만 실제로는 timeFrame에 따라 필터링
      return generateDummyData();
    });
    
    const chartData = computed(() => {
      return {
        labels: filteredData.value.labels,
        datasets: [
          {
            label: '정상 트래픽',
            data: filteredData.value.normal,
            borderColor: '#10b981',
            backgroundColor: 'rgba(16, 185, 129, 0.2)',
            tension: 0.4,
            fill: true
          },
          {
            label: '의심 트래픽',
            data: filteredData.value.suspicious,
            borderColor: '#f59e0b',
            backgroundColor: 'rgba(245, 158, 11, 0.2)',
            tension: 0.4,
            fill: true
          },
          {
            label: '악성 트래픽',
            data: filteredData.value.malicious,
            borderColor: '#ef4444',
            backgroundColor: 'rgba(239, 68, 68, 0.2)',
            tension: 0.4,
            fill: true
          }
        ]
      };
    });
    
    const chartOptions = {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        y: {
          beginAtZero: true,
          grid: {
            color: 'rgba(255, 255, 255, 0.1)'
          },
          ticks: {
            color: '#a0aec0'
          }
        },
        x: {
          grid: {
            color: 'rgba(255, 255, 255, 0.1)'
          },
          ticks: {
            color: '#a0aec0'
          }
        }
      },
      plugins: {
        legend: {
          labels: {
            color: '#a0aec0'
          }
        }
      }
    };
    
    const totalTraffic = computed(() => props.networkData.stats.totalTraffic || 1024 * 1024 * 256); // 256MB
    const requestsPerSecond = computed(() => props.networkData.stats.requestsPerSecond || 42.7);
    const abnormalConnections = computed(() => props.networkData.stats.abnormalConnections || 17);
    const blockedIPs = computed(() => props.networkData.stats.blockedIPs || 8);
    
    const geoTraffic = computed(() => props.networkData.geoTraffic.length ? props.networkData.geoTraffic : [
      { country: '대한민국', flag: '🇰🇷', percentage: 58 },
      { country: '미국', flag: '🇺🇸', percentage: 23 },
      { country: '중국', flag: '🇨🇳', percentage: 12 },
      { country: '러시아', flag: '🇷🇺', percentage: 5 },
      { country: '기타', flag: '🌍', percentage: 2 }
    ]);
    
    function formatTraffic(bytes) {
      if (bytes < 1024) return bytes + ' B';
      else if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(2) + ' KB';
      else if (bytes < 1024 * 1024 * 1024) return (bytes / 1024 / 1024).toFixed(2) + ' MB';
      else return (bytes / 1024 / 1024 / 1024).toFixed(2) + ' GB';
    }
    
    function generateDummyData() {
      // 24시간 동안의 더미 데이터 생성
      const hours = 24;
      const labels = [];
      const normal = [];
      const suspicious = [];
      const malicious = [];
      
      for (let i = 0; i < hours; i++) {
        labels.push(`${i}:00`);
        normal.push(Math.floor(Math.random() * 80) + 20);
        suspicious.push(Math.floor(Math.random() * 30));
        malicious.push(Math.floor(Math.random() * 15));
      }
      
      return { labels, normal, suspicious, malicious };
    }
    
    return {
      timeFrame,
      changeTimeFrame,
      chartData,
      chartOptions,
      totalTraffic,
      requestsPerSecond,
      abnormalConnections,
      blockedIPs,
      geoTraffic,
      formatTraffic
    };
  }
}
</script>

<style scoped>
.network-monitor {
  background: #1a1f2e;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  margin-bottom: 1.5rem;
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 1.5rem;
  background: #242938;
  border-bottom: 1px solid #343b54;
}

h2, h3 {
  margin: 0;
  font-size: 1.2rem;
  font-weight: 600;
  color: #fff;
}

h3 {
  font-size: 1rem;
  margin: 1rem 0;
}

.time-selector {
  display: flex;
  gap: 0.5rem;
}

.time-btn {
  padding: 0.35rem 0.8rem;
  border-radius: 4px;
  background: transparent;
  border: 1px solid #3f4865;
  color: #a0aec0;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s;
}

.time-btn:hover {
  background: rgba(255, 255, 255, 0.05);
}

.time-btn.active {
  background: #3f4865;
  color: #fff;
}

.chart-container {
  height: 300px;
  padding: 1.5rem;
}

.network-stats {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
  padding: 0 1.5rem 1.5rem;
}

.stat-box {
  background: #242938;
  padding: 1rem;
  border-radius: 6px;
  text-align: center;
}

.stat-label {
  color: #a0aec0;
  font-size: 0.85rem;
  margin-bottom: 0.5rem;
}

.stat-value {
  color: #fff;
  font-size: 1.4rem;
  font-weight: 700;
}

.geo-traffic {
  padding: 0 1.5rem 1.5rem;
}

.geo-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.geo-item {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.geo-flag {
  font-size: 1.2rem;
}

.geo-name {
  width: 100px;
  color: #cbd5e0;
}

.geo-bar-container {
  flex: 1;
  height: 8px;
  background: #242938;
  border-radius: 4px;
  overflow: hidden;
}

.geo-bar {
  height: 100%;
  background: linear-gradient(to right, #3b82f6, #10b981);
  border-radius: 4px;
}

.geo-percentage {
  width: 40px;
  color: #a0aec0;
  text-align: right;
}
</style> 