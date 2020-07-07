<template>
  <div class="main">
    <nuxt />
    <DropdownInput
      :units="units"
      :default-placeholder="placeholder"
      :label="defaultLabel"
    />
  </div>
</template>

<script>
import DropdownInput from '@/components/DropdownInput'
export default {
  components: { DropdownInput },
  data() {
    return {
      units: [
        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' }
      ],

      placeholder: 'Answer option',
      defaultLabel: 'First Name'
    }
  }
}
</script>

<style scoped>
@import '../components/resetStyles.scss';

.main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #e5e5e5;
}
</style>
