<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import PortalNav from '../components/PortalNav.vue'
import { checkIsFavorited, createFavorite, createMajorApplication, fetchMajor, imageUrl, type Major } from '../api/portal'
import { useAuthStore } from '../stores/auth'
import { useLanguageStore } from '../stores/language'

const route = useRoute()
const router = useRouter()
const auth = useAuthStore()
const language = useLanguageStore()
const t = language.t
const major = ref<Major | null>(null)
const message = ref('')
const error = ref('')
const loading = ref(true)
const submitting = ref<'apply' | 'favorite' | ''>('')
const isFavorited = ref(false)

const errorMessage = (caught: unknown) =>
  (caught as any)?.response?.data?.detail || (caught as Error)?.message || t('common.operationFailed')

const requireLogin = async () => {
  if (await auth.ensureSession()) {
    return true
  }
  await router.push({ path: '/login', query: { redirect: route.fullPath } })
  return false
}

const applyMajor = async () => {
  if (!major.value || !(await requireLogin())) return
  submitting.value = 'apply'
  error.value = ''
  message.value = ''
  try {
    await createMajorApplication(major.value.id)
    message.value = t('detail.majorSubmitted')
  } catch (caught) {
    error.value = errorMessage(caught)
  } finally {
    submitting.value = ''
  }
}

const favoriteMajor = async () => {
  if (!major.value || !(await requireLogin())) return
  if (isFavorited.value) {
    message.value = t('detail.alreadyFavorited')
    return
  }
  submitting.value = 'favorite'
  error.value = ''
  message.value = ''
  try {
    await createFavorite({
      targetType: 'MAJOR',
      targetId: major.value.id,
      name: major.value.name,
      picturePath: major.value.coverPath || '',
      recommendationType: major.value.code,
      remark: major.value.durationOfStudy
    })
    isFavorited.value = true
    message.value = t('detail.favoriteAdded')
  } catch (caught) {
    error.value = errorMessage(caught)
  } finally {
    submitting.value = ''
  }
}

onMounted(async () => {
  loading.value = true
  try {
    major.value = await fetchMajor(String(route.params.id))

    if (auth.isAuthenticated) {
      try {
        isFavorited.value = await checkIsFavorited('MAJOR', major.value.id)
      } catch {
        isFavorited.value = false
      }
    }
  } catch (caught) {
    error.value = errorMessage(caught)
  } finally {
    loading.value = false
  }
})
</script>
