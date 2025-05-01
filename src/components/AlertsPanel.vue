<template>
  <div class="alerts-panel">
    <div class="panel-header">
      <h2>보안 경고</h2>
      <div class="header-actions">
        <button class="btn" @click="filterAlerts('all')" :class="{ active: currentFilter === 'all' }">모두</button>
        <button class="btn" @click="filterAlerts('critical')" :class="{ active: currentFilter === 'critical' }">심각</button>
        <button class="btn" @click="filterAlerts('warning')" :class="{ active: currentFilter === 'warning' }">경고</button>
        <button class="btn" @click="filterAlerts('info')" :class="{ active: currentFilter === 'info' }">정보</button>
      </div>
    </div>
    
    <div class="alerts-list">
      <div v-if="filteredAlerts.length === 0" class="no-alerts">
        <div class="icon">🔒</div>
        <p>현재 경고가 없습니다</p>
      </div>
      
      <div v-for="alert in filteredAlerts" :key="alert.id" class="alert-item" :class="alert.severity">
        <div class="alert-icon">
          <span v-if="alert.severity === 'critical'">⚠️</span>
          <span v-else-if="alert.severity === 'warning'">⚠</span>
          <span v-else>ℹ️</span>
        </div>
        <div class="alert-content">
          <div class="alert-title">{{ alert.title }}</div>
          <div class="alert-meta">
            <span class="alert-time">{{ formatTime(alert.timestamp) }}</span>
            <span class="alert-source">{{ alert.source }}</span>
            <span class="alert-ip">{{ alert.sourceIP }}</span>
          </div>
          <p class="alert-message">{{ alert.message }}</p>
        </div>
        <div class="alert-actions">
          <button class="action-btn" @click="resolveAlert(alert.id)">해결</button>
          <button class="action-btn" @click="blockIP(alert.sourceIP)">IP 차단</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { format } from 'date-fns';

export default {
  name: 'AlertsPanel',
  props: {
    alerts: {
      type: Array,
      default: () => []
    }
  },
  setup(props, { emit }) {
    const currentFilter = ref('all');
    
    const filteredAlerts = computed(() => {
      if (currentFilter.value === 'all') {
        return props.alerts;
      }
      return props.alerts.filter(alert => alert.severity === currentFilter.value);
    });
    
    function filterAlerts(filter) {
      currentFilter.value = filter;
    }
    
    function formatTime(timestamp) {
      return format(new Date(timestamp), 'HH:mm:ss');
    }
    
    function resolveAlert(id) {
      emit('resolve', id);
    }
    
    function blockIP(ip) {
      emit('block-ip', ip);
    }
    
    return {
      currentFilter,
      filteredAlerts,
      filterAlerts,
      formatTime,
      resolveAlert,
      blockIP
    };
  }
}
</script>

<style scoped>
.alerts-panel {
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

.header-actions {
  display: flex;
  gap: 0.5rem;
}

.btn {
  padding: 0.35rem 0.8rem;
  border-radius: 4px;
  background: transparent;
  border: 1px solid #3f4865;
  color: #a0aec0;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s;
}

.btn:hover {
  background: rgba(255, 255, 255, 0.05);
}

.btn.active {
  background: #3f4865;
  color: #fff;
}

.alerts-list {
  max-height: 500px;
  overflow-y: auto;
  padding: 1rem;
}

.no-alerts {
  text-align: center;
  padding: 3rem 1rem;
  color: #a0aec0;
}

.no-alerts .icon {
  font-size: 2rem;
  margin-bottom: 1rem;
}

.alert-item {
  display: flex;
  align-items: flex-start;
  padding: 1rem;
  border-radius: 6px;
  margin-bottom: 0.75rem;
  background: #242938;
  border-left: 4px solid transparent;
  transition: all 0.2s;
}

.alert-item:hover {
  background: #2a3040;
}

.alert-item.critical {
  border-left-color: #ef4444;
  background-color: rgba(239, 68, 68, 0.1);
}

.alert-item.warning {
  border-left-color: #f59e0b;
  background-color: rgba(245, 158, 11, 0.1);
}

.alert-item.info {
  border-left-color: #3b82f6;
  background-color: rgba(59, 130, 246, 0.1);
}

.alert-icon {
  margin-right: 1rem;
  font-size: 1.2rem;
}

.alert-content {
  flex: 1;
}

.alert-title {
  font-weight: 600;
  color: #fff;
  margin-bottom: 0.3rem;
}

.alert-meta {
  display: flex;
  gap: 1rem;
  font-size: 0.8rem;
  color: #a0aec0;
  margin-bottom: 0.75rem;
}

.alert-message {
  color: #cbd5e0;
  margin: 0;
  font-size: 0.95rem;
}

.alert-actions {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-left: 1rem;
}

.action-btn {
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

.action-btn:hover {
  background: #3f4865;
  color: #fff;
}
</style> 