<script>
	import appStore from 'themekit-docs/src/js/app.store'
	import { AlertNotification } from 'themekit-vue'
	import store from 'themekit-docs-store'

	export default {
		mixins: [
			AlertNotification
		],
		data () {
			return {
				appConfig: appStore.config,
				serviceLoading: false,
				serviceCompleteDebounce: null
			}
		},
		methods: {
			onServiceComplete () {
				clearTimeout(this.serviceCompleteDebounce)
				this.serviceCompleteDebounce = setTimeout(() => {
					this.serviceLoading = false
				}, 250)
			},
			onServiceError (e) {
				this.alertNotificationError(e)
			},
			onServiceLoading () {
				this.serviceLoading = true
			}
		},
		created () {
			store.setRef(this.appConfig.storeFirebaseRef)
			store.on('serviceLoading', this.onServiceLoading)
			store.on('serviceComplete', this.onServiceComplete)
			store.on('serviceError', this.onServiceError)
			this.store = store
		},
		destroyed () {
			store.ref.off()
			store.removeListener('serviceLoading', this.onServiceLoading)
			store.removeListener('serviceComplete', this.onServiceComplete)
			store.removeListener('serviceError', this.onServiceError)
			clearTimeout(this.serviceCompleteDebounce)
		},
		watch: {
			serviceLoading (value) {
				if (value) {
					$('button[type="submit"]').prop('disabled', true)
				}
				else {
					$('button[type="submit"]').prop('disabled', false)
				}
			}
		}
	}
</script>