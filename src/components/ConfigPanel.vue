<template>
  <div class="config-panel">
    <div class="panel-header">
      <h2>시스템 설정</h2>
      <div class="header-actions">
        <button class="btn" @click="saveConfig">저장</button>
        <button class="btn" @click="resetDefaults">기본값 복원</button>
      </div>
    </div>
    
    <div class="config-sections">
      <div class="config-section">
        <h3>보안 설정</h3>
        
        <div class="config-group">
          <label class="config-label">보안 레벨</label>
          <div class="security-level-selector">
            <button
              v-for="level in securityLevels"
              :key="level.value"
              class="security-btn"
              :class="{ active: config.securityLevel === level.value }"
              @click="config.securityLevel = level.value"
            >
              {{ level.label }}
            </button>
          </div>
        </div>
        
        <div class="config-group">
          <div class="toggle-switch">
            <input type="checkbox" id="auto-update" v-model="config.autoUpdate">
            <label for="auto-update">자동 업데이트</label>
          </div>
          <span class="config-desc">최신 보안 정의 및 패치를 자동으로 적용합니다.</span>
        </div>
        
        <div class="config-group">
          <div class="toggle-switch">
            <input type="checkbox" id="auto-block" v-model="config.autoBlockAttackers">
            <label for="auto-block">악성 IP 자동 차단</label>
          </div>
          <span class="config-desc">여러 번 의심스러운 행동을 시도한 IP를 자동으로 차단합니다.</span>
        </div>
        
        <div class="config-group">
          <div class="toggle-switch">
            <input type="checkbox" id="enhanced-logging" v-model="config.enhancedLogging">
            <label for="enhanced-logging">고급 로깅</label>
          </div>
          <span class="config-desc">보다 자세한 로깅을 활성화합니다. 디스크 공간을 더 사용합니다.</span>
        </div>
      </div>
      
      <div class="config-section">
        <h3>알림 설정</h3>
        
        <div class="config-group">
          <label class="config-label">알림 방식</label>
          <div class="select-container">
            <select v-model="config.notificationMethod">
              <option value="email">이메일</option>
              <option value="sms">SMS</option>
              <option value="both">이메일 + SMS</option>
              <option value="none">없음</option>
            </select>
          </div>
        </div>
        
        <div class="config-group" v-if="config.notificationMethod !== 'none'">
          <label class="config-label">알림 심각도 임계값</label>
          <div class="select-container">
            <select v-model="config.notificationThreshold">
              <option value="low">낮음 (모든 알림)</option>
              <option value="medium">중간 (중요 이상)</option>
              <option value="high">높음 (심각만)</option>
            </select>
          </div>
        </div>
        
        <div class="config-group" v-if="config.notificationMethod.includes('email')">
          <label class="config-label">이메일 주소</label>
          <input type="email" v-model="config.emailAddress" placeholder="admin@example.com">
        </div>
        
        <div class="config-group" v-if="config.notificationMethod.includes('sms')">
          <label class="config-label">전화번호</label>
          <input type="tel" v-model="config.phoneNumber" placeholder="+82 10-1234-5678">
        </div>
      </div>
      
      <div class="config-section">
        <h3>스캔 설정</h3>
        
        <div class="config-group">
          <label class="config-label">스캔 빈도</label>
          <div class="select-container">
            <select v-model="config.scanFrequency">
              <option value="hourly">매시간</option>
              <option value="daily">매일</option>
              <option value="weekly">매주</option>
              <option value="monthly">매월</option>
            </select>
          </div>
        </div>
        
        <div class="config-group">
          <label class="config-label">스캔 강도</label>
          <div class="range-slider">
            <input type="range" min="1" max="5" v-model="config.scanIntensity">
            <div class="range-values">
              <span>낮음</span>
              <span>중간</span>
              <span>높음</span>
            </div>
          </div>
        </div>
        
        <div class="config-group">
          <label class="config-label">사용자 지정 스캔 시간</label>
          <input type="time" v-model="config.customScanTime">
        </div>
        
        <div class="config-group">
          <div class="toggle-switch">
            <input type="checkbox" id="deep-scan" v-model="config.deepScan">
            <label for="deep-scan">심층 스캔</label>
          </div>
          <span class="config-desc">더 철저한 심층 스캔을 활성화합니다. 시스템 자원을 더 사용합니다.</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive } from 'vue';

export default {
  name: 'ConfigPanel',
  emits: ['save-config', 'reset-config'],
  setup(props, { emit }) {
    const securityLevels = [
      { value: 'low', label: '낮음' },
      { value: 'medium', label: '중간' },
      { value: 'high', label: '높음' },
      { value: 'custom', label: '사용자 지정' }
    ];
    
    const defaultConfig = {
      securityLevel: 'medium',
      autoUpdate: true,
      autoBlockAttackers: true,
      enhancedLogging: false,
      notificationMethod: 'email',
      notificationThreshold: 'medium',
      emailAddress: 'admin@example.com',
      phoneNumber: '',
      scanFrequency: 'daily',
      scanIntensity: 3,
      customScanTime: '03:00',
      deepScan: false
    };
    
    const config = reactive({ ...defaultConfig });
    
    function saveConfig() {
      emit('save-config', { ...config });
    }
    
    function resetDefaults() {
      Object.assign(config, defaultConfig);
      emit('reset-config');
    }
    
    return {
      securityLevels,
      config,
      saveConfig,
      resetDefaults
    };
  }
}
</script>

<style scoped>
.config-panel {
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

h2, h3 {
  margin: 0;
  font-size: 1.2rem;
  font-weight: 600;
  color: #fff;
}

h3 {
  font-size: 1rem;
  margin-bottom: 1.2rem;
}

.header-actions {
  display: flex;
  gap: 0.8rem;
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

.config-sections {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  padding: 1.5rem;
}

.config-section {
  padding: 1.5rem;
  background: #242938;
  border-radius: 6px;
}

.config-group {
  margin-bottom: 1.5rem;
}

.config-label {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 0.9rem;
  color: #cbd5e0;
}

.config-desc {
  display: block;
  font-size: 0.8rem;
  color: #718096;
  margin-top: 0.35rem;
}

.select-container {
  position: relative;
}

select {
  width: 100%;
  padding: 0.6rem 0.8rem;
  border-radius: 4px;
  background: #1a1f2e;
  border: 1px solid #3f4865;
  color: #cbd5e0;
  font-size: 0.9rem;
  appearance: none;
  -webkit-appearance: none;
  cursor: pointer;
}

.select-container::after {
  content: "▼";
  font-size: 0.7rem;
  position: absolute;
  right: 0.8rem;
  top: 50%;
  transform: translateY(-50%);
  color: #cbd5e0;
  pointer-events: none;
}

input[type="email"],
input[type="tel"],
input[type="time"] {
  width: 100%;
  padding: 0.6rem 0.8rem;
  border-radius: 4px;
  background: #1a1f2e;
  border: 1px solid #3f4865;
  color: #cbd5e0;
  font-size: 0.9rem;
}

.toggle-switch {
  display: flex;
  align-items: center;
}

.toggle-switch input[type="checkbox"] {
  position: absolute;
  opacity: 0;
  width: 0;
  height: 0;
}

.toggle-switch label {
  position: relative;
  display: inline-block;
  padding-left: 3.5rem;
  cursor: pointer;
  color: #cbd5e0;
  font-size: 0.9rem;
}

.toggle-switch label::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  width: 2.8rem;
  height: 1.4rem;
  border-radius: 1rem;
  background: #1a1f2e;
  border: 1px solid #3f4865;
  transition: all 0.2s;
}

.toggle-switch label::after {
  content: '';
  position: absolute;
  left: 0.2rem;
  top: 0.2rem;
  width: 1rem;
  height: 1rem;
  border-radius: 50%;
  background: #cbd5e0;
  transition: all 0.2s;
}

.toggle-switch input:checked + label::before {
  background: #10b981;
  border-color: #10b981;
}

.toggle-switch input:checked + label::after {
  left: 1.6rem;
  background: #fff;
}

.security-level-selector {
  display: flex;
  gap: 0.5rem;
}

.security-btn {
  flex: 1;
  padding: 0.5rem;
  border-radius: 4px;
  background: #1a1f2e;
  border: 1px solid #3f4865;
  color: #a0aec0;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s;
}

.security-btn:hover {
  background: rgba(255, 255, 255, 0.05);
}

.security-btn.active {
  background: #3f4865;
  color: #fff;
}

.range-slider {
  margin-top: 0.5rem;
}

.range-slider input[type="range"] {
  width: 100%;
  -webkit-appearance: none;
  height: 6px;
  border-radius: 3px;
  background: #1a1f2e;
  outline: none;
}

.range-slider input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #10b981;
  cursor: pointer;
}

.range-values {
  display: flex;
  justify-content: space-between;
  margin-top: 0.5rem;
  font-size: 0.8rem;
  color: #718096;
}
</style> 