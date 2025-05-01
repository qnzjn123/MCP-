<template>
  <header class="ids-header">
    <div class="logo">
      <h1>MCP 침입 탐지 시스템</h1>
      <span class="badge" :class="systemStatus">{{ systemStatusText }}</span>
    </div>
    <div class="stats">
      <div class="stat-item">
        <div class="stat-value">{{ totalAlerts }}</div>
        <div class="stat-label">총 경고</div>
      </div>
      <div class="stat-item">
        <div class="stat-value">{{ criticalAlerts }}</div>
        <div class="stat-label">심각한 경고</div>
      </div>
      <div class="stat-item">
        <div class="stat-value">{{ activeSessions }}</div>
        <div class="stat-label">활성 세션</div>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'IDSHeader',
  props: {
    systemStatus: {
      type: String,
      default: 'normal',
      validator: (value) => ['normal', 'warning', 'critical'].includes(value)
    },
    totalAlerts: {
      type: Number,
      default: 0
    },
    criticalAlerts: {
      type: Number,
      default: 0
    },
    activeSessions: {
      type: Number,
      default: 0
    }
  },
  computed: {
    systemStatusText() {
      const statusMap = {
        normal: '정상',
        warning: '주의',
        critical: '위험'
      };
      return statusMap[this.systemStatus] || '알 수 없음';
    }
  }
}
</script>

<style scoped>
.ids-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  background: #1e2132;
  color: #fff;
  border-bottom: 1px solid #343b54;
}

.logo {
  display: flex;
  align-items: center;
}

h1 {
  font-size: 1.8rem;
  margin: 0;
  margin-right: 1rem;
  font-weight: 600;
}

.badge {
  padding: 0.3rem 0.8rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  text-transform: uppercase;
}

.normal {
  background: #10b981;
}

.warning {
  background: #f59e0b;
}

.critical {
  background: #ef4444;
  animation: pulse 2s infinite;
}

.stats {
  display: flex;
  gap: 2rem;
}

.stat-item {
  text-align: center;
}

.stat-value {
  font-size: 1.8rem;
  font-weight: 700;
}

.stat-label {
  font-size: 0.8rem;
  opacity: 0.8;
  text-transform: uppercase;
}

@keyframes pulse {
  0% { opacity: 1; }
  50% { opacity: 0.7; }
  100% { opacity: 1; }
}
</style> 