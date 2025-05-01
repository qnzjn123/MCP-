<template>
  <div class="ids-services">
    <div class="panel-header">
      <h2>MCP 서비스 상태</h2>
      <button class="refresh-btn" @click="refreshServices">
        새로고침
      </button>
    </div>
    
    <div class="services-grid">
      <div v-for="service in services" :key="service.id" class="service-card" :class="{ 'service-down': !service.isRunning }">
        <div class="service-icon" :class="service.type">
          <span v-if="service.type === 'firewall'">🛡️</span>
          <span v-else-if="service.type === 'monitor'">📊</span>
          <span v-else-if="service.type === 'scanner'">🔍</span>
          <span v-else-if="service.type === 'analyzer'">🧠</span>
          <span v-else>⚙️</span>
        </div>
        <div class="service-info">
          <div class="service-name">{{ service.name }}</div>
          <div class="service-desc">{{ service.description }}</div>
          <div class="service-meta">
            <div class="service-uptime" v-if="service.isRunning">
              <span class="uptime-label">가동 시간:</span> {{ formatUptime(service.uptime) }}
            </div>
            <div class="service-downtime" v-else>
              <span class="downtime-label">중단 시간:</span> {{ formatUptime(service.downtime) }}
            </div>
          </div>
        </div>
        <div class="service-status">
          <div class="status-indicator" :class="{ 'status-running': service.isRunning, 'status-down': !service.isRunning }">
            {{ service.isRunning ? '정상' : '중단' }}
          </div>
          <button class="service-btn" @click="toggleService(service.id)">
            {{ service.isRunning ? '중지' : '시작' }}
          </button>
          <button class="service-btn" @click="configureService(service.id)">
            설정
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'IDSServices',
  emits: ['start-service', 'stop-service', 'configure-service'],
  setup(props, { emit }) {
    const services = ref([
      {
        id: 1,
        name: 'MCP 방화벽 서비스',
        description: '악성 트래픽을 차단하고 네트워크 액세스를 제어합니다.',
        type: 'firewall',
        isRunning: true,
        uptime: 672400, // 초 단위
        downtime: 0
      },
      {
        id: 2,
        name: '네트워크 활동 모니터',
        description: '실시간 네트워크 트래픽을 모니터링하고 로깅합니다.',
        type: 'monitor',
        isRunning: true,
        uptime: 348200,
        downtime: 0
      },
      {
        id: 3,
        name: '취약점 스캐너',
        description: '시스템 취약점을 주기적으로 스캔하고 보고합니다.',
        type: 'scanner',
        isRunning: true,
        uptime: 127800,
        downtime: 0
      },
      {
        id: 4,
        name: '이상 행동 분석기',
        description: '패턴 분석 및 기계학습을 통해 이상 행동을 탐지합니다.',
        type: 'analyzer',
        isRunning: false,
        uptime: 0,
        downtime: 4500
      },
      {
        id: 5,
        name: '로그 수집 및 분석',
        description: '시스템 전체 로그를 수집하고 분석합니다.',
        type: 'logger',
        isRunning: true,
        uptime: 427900,
        downtime: 0
      },
      {
        id: 6,
        name: '침입 대응 시스템',
        description: '침입이 감지되면 자동으로 대응 조치를 취합니다.',
        type: 'response',
        isRunning: true,
        uptime: 243600,
        downtime: 0
      }
    ]);
    
    function formatUptime(seconds) {
      const days = Math.floor(seconds / 86400);
      const hours = Math.floor((seconds % 86400) / 3600);
      const minutes = Math.floor((seconds % 3600) / 60);
      
      if (days > 0) {
        return `${days}일 ${hours}시간`;
      } else if (hours > 0) {
        return `${hours}시간 ${minutes}분`;
      } else {
        return `${minutes}분`;
      }
    }
    
    function toggleService(id) {
      const serviceIndex = services.value.findIndex(s => s.id === id);
      if (serviceIndex > -1) {
        const service = services.value[serviceIndex];
        const isRunning = !service.isRunning;
        
        // 서비스 상태 토글
        services.value[serviceIndex] = {
          ...service,
          isRunning
        };
        
        // 이벤트 발생
        if (isRunning) {
          emit('start-service', id);
        } else {
          emit('stop-service', id);
        }
      }
    }
    
    function configureService(id) {
      emit('configure-service', id);
    }
    
    function refreshServices() {
      // 실제로는 서버에서 서비스 상태를 다시 가져오는 API 호출을 하겠지만
      // 이 예제에서는 시뮬레이션을 위해 서비스 가동시간을 업데이트
      services.value = services.value.map(service => {
        if (service.isRunning) {
          return { ...service, uptime: service.uptime + 300 };
        } else {
          return { ...service, downtime: service.downtime + 300 };
        }
      });
    }
    
    return {
      services,
      formatUptime,
      toggleService,
      configureService,
      refreshServices
    };
  }
}
</script>

<style scoped>
.ids-services {
  background: #1a1f2e;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 1.5rem;
  background: #242938;
  border-bottom: 1px solid #343b54;
}

h2 {
  margin: 0;
  font-size: 1.2rem;
  font-weight: 600;
  color: #fff;
}

.refresh-btn {
  padding: 0.35rem 0.8rem;
  border-radius: 4px;
  background: transparent;
  border: 1px solid #3f4865;
  color: #a0aec0;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s;
}

.refresh-btn:hover {
  background: #3f4865;
  color: #fff;
}

.services-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
  gap: 1rem;
  padding: 1.5rem;
}

.service-card {
  display: flex;
  background: #242938;
  border-radius: 6px;
  padding: 1.2rem;
  position: relative;
  overflow: hidden;
  transition: all 0.3s;
  border-left: 4px solid #10b981;
}

.service-down {
  border-left-color: #ef4444;
}

.service-down::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: repeating-linear-gradient(
    45deg,
    rgba(239, 68, 68, 0.05),
    rgba(239, 68, 68, 0.05) 10px,
    rgba(239, 68, 68, 0.1) 10px,
    rgba(239, 68, 68, 0.1) 20px
  );
  z-index: 0;
}

.service-icon {
  font-size: 1.8rem;
  margin-right: 1.2rem;
  background: #1a1f2e;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
  z-index: 1;
}

.service-info {
  flex: 1;
  z-index: 1;
}

.service-name {
  font-weight: 600;
  color: #fff;
  margin-bottom: 0.3rem;
}

.service-desc {
  color: #a0aec0;
  font-size: 0.9rem;
  margin-bottom: 0.75rem;
}

.service-meta {
  display: flex;
  font-size: 0.8rem;
  color: #a0aec0;
}

.uptime-label, .downtime-label {
  opacity: 0.7;
  margin-right: 0.5rem;
}

.service-status {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 0.5rem;
  z-index: 1;
}

.status-indicator {
  padding: 0.25rem 0.6rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  text-transform: uppercase;
}

.status-running {
  background: rgba(16, 185, 129, 0.2);
  color: #10b981;
}

.status-down {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
}

.service-btn {
  padding: 0.35rem 0.8rem;
  border-radius: 4px;
  background: transparent;
  border: 1px solid #3f4865;
  color: #a0aec0;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 0.2s;
  white-space: nowrap;
}

.service-btn:hover {
  background: #3f4865;
  color: #fff;
}
</style> 