<template>
  <div class="app">
    <IDSHeader 
      :systemStatus="systemStatus" 
      :totalAlerts="alerts.length" 
      :criticalAlerts="criticalAlertsCount" 
      :activeSessions="activeSessions"
    />
    
    <main class="main-content">
      <div class="content-row">
        <AlertsPanel 
          :alerts="alerts" 
          @resolve="resolveAlert"
          @block-ip="blockIP"
        />
        <NetworkMonitor :networkData="networkData" />
      </div>
      
      <div class="content-row">
        <IDSServices 
          @start-service="startService"
          @stop-service="stopService"
          @configure-service="configureService"
        />
      </div>
      
      <div v-if="showConfig" class="content-row">
        <ConfigPanel 
          @save-config="saveConfig"
          @reset-config="resetConfig"
        />
      </div>
    </main>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import IDSHeader from './components/IDSHeader.vue';
import AlertsPanel from './components/AlertsPanel.vue';
import NetworkMonitor from './components/NetworkMonitor.vue';
import IDSServices from './components/IDSServices.vue';
import ConfigPanel from './components/ConfigPanel.vue';

export default {
  name: 'App',
  components: {
    IDSHeader,
    AlertsPanel,
    NetworkMonitor,
    IDSServices,
    ConfigPanel
  },
  setup() {
    // 시스템 상태
    const systemStatus = ref('normal'); // 'normal', 'warning', 'critical'
    const activeSessions = ref(24);
    const showConfig = ref(false);
    
    // 경고 관련 데이터
    const alerts = ref([
      {
        id: 1,
        title: '다중 인증 실패',
        message: '단일 IP에서 다중 인증 실패가 감지되었습니다.',
        severity: 'warning',
        timestamp: Date.now() - 300000,
        source: '인증 서비스',
        sourceIP: '192.168.1.105'
      },
      {
        id: 2,
        title: '의심스러운 파일 업로드 시도',
        message: '잠재적 악성 코드를 포함한 파일 업로드가 차단되었습니다.',
        severity: 'critical',
        timestamp: Date.now() - 1200000,
        source: '파일 서버',
        sourceIP: '192.168.1.57'
      },
      {
        id: 3,
        title: '비정상적인 네트워크 트래픽',
        message: '알려진 C&C 서버로 향하는 의심스러운 트래픽이 감지되었습니다.',
        severity: 'critical',
        timestamp: Date.now() - 7200000,
        source: '네트워크 모니터',
        sourceIP: '192.168.1.32'
      },
      {
        id: 4,
        title: '권한 상승 시도',
        message: '사용자 계정에서 권한 상승 시도가 감지되었습니다.',
        severity: 'warning',
        timestamp: Date.now() - 18000000,
        source: '권한 관리자',
        sourceIP: '192.168.1.18'
      },
      {
        id: 5,
        title: '시스템 구성 변경',
        message: '중요 시스템 구성 파일이 변경되었습니다.',
        severity: 'info',
        timestamp: Date.now() - 86400000,
        source: '파일 무결성 모니터',
        sourceIP: '192.168.1.10'
      }
    ]);
    
    // 임시 네트워크 데이터
    const networkData = ref({
      traffic: [],
      geoTraffic: [
        { country: '대한민국', flag: '🇰🇷', percentage: 58 },
        { country: '미국', flag: '🇺🇸', percentage: 23 },
        { country: '중국', flag: '🇨🇳', percentage: 12 },
        { country: '러시아', flag: '🇷🇺', percentage: 5 },
        { country: '기타', flag: '🌍', percentage: 2 }
      ],
      stats: {
        totalTraffic: 1024 * 1024 * 256, // 256MB
        requestsPerSecond: 42.7,
        abnormalConnections: 17,
        blockedIPs: 8
      }
    });
    
    // 계산된 속성
    const criticalAlertsCount = computed(() => {
      return alerts.value.filter(alert => alert.severity === 'critical').length;
    });
    
    // 경고 해결 처리
    function resolveAlert(id) {
      const index = alerts.value.findIndex(alert => alert.id === id);
      if (index !== -1) {
        alerts.value.splice(index, 1);
        updateSystemStatus();
      }
    }
    
    // IP 차단 처리
    function blockIP(ip) {
      // 실제 구현에서는 여기서 IP 차단 API 호출
      console.log(`IP ${ip} 차단됨`);
      networkData.value.stats.blockedIPs++;
      
      // 해당 IP의 모든 경고 제거
      alerts.value = alerts.value.filter(alert => alert.sourceIP !== ip);
      updateSystemStatus();
    }
    
    // 서비스 제어 함수
    function startService(id) {
      console.log(`서비스 #${id} 시작됨`);
    }
    
    function stopService(id) {
      console.log(`서비스 #${id} 중지됨`);
    }
    
    function configureService(id) {
      showConfig.value = true;
      console.log(`서비스 #${id} 설정 열림`);
    }
    
    // 설정 관련 함수
    function saveConfig(config) {
      console.log('설정 저장됨', config);
      showConfig.value = false;
    }
    
    function resetConfig() {
      console.log('설정 초기화됨');
    }
    
    // 시스템 상태 업데이트
    function updateSystemStatus() {
      const criticalCount = criticalAlertsCount.value;
      const warningCount = alerts.value.filter(alert => alert.severity === 'warning').length;
      
      if (criticalCount > 0) {
        systemStatus.value = 'critical';
      } else if (warningCount > 0) {
        systemStatus.value = 'warning';
      } else {
        systemStatus.value = 'normal';
      }
    }
    
    // 시뮬레이션 알림 추가
    function addRandomAlert() {
      const alertTypes = [
        {
          title: '무차별 대입 공격 시도',
          message: '여러 계정에 대한 무차별 대입 공격이 감지되었습니다.',
          severity: 'critical',
          source: '인증 서비스'
        },
        {
          title: '알려진 취약점 스캔',
          message: '시스템 취약점에 대한 스캔 시도가 감지되었습니다.',
          severity: 'warning',
          source: '네트워크 모니터'
        },
        {
          title: '데이터베이스 쿼리 주입 시도',
          message: 'SQL 주입 공격 패턴이 감지되었습니다.',
          severity: 'critical',
          source: '웹 애플리케이션 방화벽'
        },
        {
          title: '비정상적인 로그인 위치',
          message: '사용자 계정이 비정상적인 지리적 위치에서 로그인되었습니다.',
          severity: 'warning',
          source: '인증 서비스'
        },
        {
          title: '비정상적인 API 호출 패턴',
          message: '단일 클라이언트에서 API 호출 속도 제한이 초과되었습니다.',
          severity: 'info',
          source: 'API 게이트웨이'
        }
      ];
      
      const randomAlert = alertTypes[Math.floor(Math.random() * alertTypes.length)];
      const randomIP = `192.168.1.${Math.floor(Math.random() * 255)}`;
      
      const newAlert = {
        id: Date.now(),
        title: randomAlert.title,
        message: randomAlert.message,
        severity: randomAlert.severity,
        timestamp: Date.now(),
        source: randomAlert.source,
        sourceIP: randomIP
      };
      
      alerts.value.unshift(newAlert);
      updateSystemStatus();
      
      // 경고 수 제한
      if (alerts.value.length > 20) {
        alerts.value.pop();
      }
    }
    
    // 컴포넌트 마운트 시 시뮬레이션 시작
    onMounted(() => {
      // 주기적으로 임의의 경고 추가 (시뮬레이션용)
      setInterval(addRandomAlert, 30000);
      
      // 초기 시스템 상태 설정
      updateSystemStatus();
    });
    
    return {
      systemStatus,
      alerts,
      networkData,
      activeSessions,
      criticalAlertsCount,
      resolveAlert,
      blockIP,
      startService,
      stopService,
      configureService,
      saveConfig,
      resetConfig,
      showConfig
    };
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Inter', 'Noto Sans KR', sans-serif;
  line-height: 1.6;
  color: #e2e8f0;
  background: #131723;
}

.app {
  max-width: 1440px;
  margin: 0 auto;
  padding: 1rem;
}

.main-content {
  padding: 1.5rem 0;
}

.content-row {
  margin-bottom: 1.5rem;
}

@media (max-width: 768px) {
  .content-row {
    flex-direction: column;
  }
}
</style> 