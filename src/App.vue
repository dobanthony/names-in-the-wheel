<template>
  <div class="min-h-screen bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500 p-3 md:p-4">
    <div class="w-full h-full flex flex-col lg:flex-row gap-3 md:gap-4">
      
      <!-- Left Column: The Wheel - Full width on mobile, 80% on desktop -->
      <div class="w-full lg:w-[80%] bg-white rounded-2xl md:rounded-3xl shadow-2xl p-3 md:p-4 lg:p-6 flex flex-col">
        
        <!-- Header Bar with Name Count and Sound Toggle -->
        <div class="flex items-center justify-between mb-2 md:mb-4">
          <h2 class="text-lg md:text-xl lg:text-2xl font-bold text-gray-800 flex items-center gap-1 md:gap-2">
            <svg class="w-5 h-5 md:w-6 md:h-6 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
            </svg>
            <span class="hidden sm:inline">Names in the Wheel</span>
            <span class="sm:hidden">Wheel</span>
          </h2>
          
          <div class="flex items-center gap-2 md:gap-3">
            <!-- Name Count Badge -->
            <div class="flex items-center gap-1 md:gap-1.5 px-2 md:px-3 py-1 md:py-1.5 bg-indigo-50 rounded-full">
              <svg class="w-3.5 h-3.5 md:w-4 md:h-4 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"></path>
              </svg>
              <span class="text-indigo-700 font-semibold text-xs md:text-sm">{{ names.length }}</span>
            </div>
            
            <!-- Sound Toggle Icon Only -->
            <button
              @click="soundEnabled = !soundEnabled"
              class="w-7 h-7 md:w-9 md:h-9 flex items-center justify-center rounded-full transition-colors"
              :class="soundEnabled ? 'bg-indigo-100 text-indigo-600 hover:bg-indigo-200' : 'bg-gray-100 text-gray-400 hover:bg-gray-200'"
              :title="soundEnabled ? 'Sound On' : 'Sound Off'"
            >
              <svg v-if="soundEnabled" class="w-4 h-4 md:w-5 md:h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path>
              </svg>
              <svg v-else class="w-4 h-4 md:w-5 md:h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path>
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2"></path>
              </svg>
            </button>
          </div>
        </div>

        <!-- Canvas Container - Responsive sizing -->
        <div class="relative flex-1 flex items-center justify-center min-h-0 py-2">
          <div class="relative w-full max-w-[650px] mx-auto" ref="canvasContainer">
            <canvas
              ref="wheelCanvas"
              :width="canvasSize"
              :height="canvasSize"
              class="w-full h-auto object-contain cursor-pointer"
              :class="{ 'animate-pulse': isSpinning }"
              @click="spinWheel"
            ></canvas>
            
            <!-- Center Play Button - Responsive sizing -->
            <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2">
              <button
                @click="spinWheel"
                :disabled="isSpinning"
                class="w-12 h-12 sm:w-16 sm:h-16 md:w-20 md:h-20 bg-white rounded-full shadow-2xl border-3 md:border-4 border-indigo-600 flex items-center justify-center transform hover:scale-110 transition-all disabled:opacity-50 disabled:cursor-not-allowed disabled:hover:scale-100"
              >
                <!-- Play Icon -->
                <svg v-if="!isSpinning" class="w-6 h-6 sm:w-8 sm:h-8 md:w-10 md:h-10 text-indigo-600 ml-0.5 md:ml-1" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M8 5v14l11-7z"/>
                </svg>
                <!-- Spinner Icon -->
                <svg v-else class="w-6 h-6 sm:w-8 sm:h-8 md:w-10 md:h-10 text-indigo-600 animate-spin" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
              </button>
            </div>
            
            <!-- Pointer Arrow - Responsive sizing -->
            <div class="absolute -top-0.5 md:-top-1 left-1/2 -translate-x-1/2">
              <div class="w-0 h-0 border-l-[16px] sm:border-l-[20px] md:border-l-[24px] border-l-transparent border-r-[16px] sm:border-r-[20px] md:border-r-[24px] border-r-transparent border-t-[24px] sm:border-t-[30px] md:border-t-[36px] border-t-red-500 drop-shadow-lg"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Right Column: Name Management - Full width on mobile, 20% on desktop -->
      <div class="w-full lg:w-[20%] bg-white rounded-2xl md:rounded-3xl shadow-2xl p-3 md:p-4 flex flex-col min-h-0 lg:min-w-[260px]">
        <h2 class="text-base md:text-lg font-bold text-gray-800 mb-2 md:mb-3 flex items-center gap-1">
          <svg class="w-4 h-4 md:w-5 md:h-5 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path>
          </svg>
          <span>Name List</span>
        </h2>
        
        <!-- Add Name Form - Compact on mobile -->
        <form @submit.prevent="addName" class="mb-2 md:mb-3">
          <input
            v-model="newName"
            type="text"
            placeholder="Enter name..."
            class="w-full px-2.5 md:px-3 py-2 md:py-2.5 text-sm border-2 border-gray-200 rounded-xl focus:border-indigo-500 focus:outline-none transition-colors mb-1.5 md:mb-2"
            :disabled="isSpinning"
          />
          <button
            type="submit"
            :disabled="!newName.trim() || isSpinning"
            class="w-full py-2 md:py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white text-xs md:text-sm font-semibold rounded-xl transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-1"
          >
            <svg class="w-3.5 h-3.5 md:w-4 md:h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Add Name
          </button>
        </form>

        <!-- Quick Actions -->
        <div class="flex gap-1.5 md:gap-2 mb-2 md:mb-3">
          <button
            @click="shuffleDefaultNames"
            :disabled="isSpinning"
            class="flex-1 py-1.5 md:py-2 px-1.5 md:px-2 bg-gray-100 hover:bg-gray-200 text-gray-700 rounded-lg text-xs font-medium transition-colors disabled:opacity-50 flex items-center justify-center gap-1"
          >
            <svg class="w-3 h-3 md:w-3.5 md:h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
            </svg>
            <span class="hidden xs:inline">Shuffle</span>
          </button>
          <button
            @click="confirmClearAll"
            :disabled="isSpinning"
            class="flex-1 py-1.5 md:py-2 px-1.5 md:px-2 bg-red-50 hover:bg-red-100 text-red-600 rounded-lg text-xs font-medium transition-colors disabled:opacity-50 flex items-center justify-center gap-1"
          >
            <svg class="w-3 h-3 md:w-3.5 md:h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
            </svg>
            <span class="hidden xs:inline">Delete All</span>
          </button>
        </div>

        <!-- Bulk Import Toggle -->
        <button
          @click="showBulkImport = !showBulkImport"
          class="text-indigo-600 hover:text-indigo-800 text-xs font-medium flex items-center gap-1 mb-1.5 md:mb-2"
        >
          <svg class="w-3 h-3 md:w-3.5 md:h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="showBulkImport ? 'M19 9l-7 7-7-7' : 'M9 5l7 7-7 7'"></path>
          </svg>
          Bulk Import (comma)
        </button>
        
        <!-- Bulk Import Area -->
        <div v-if="showBulkImport" class="mb-2 md:mb-3">
          <textarea
            v-model="bulkNames"
            rows="2"
            placeholder="John, Jane, Alex..."
            class="w-full px-2.5 md:px-3 py-1.5 md:py-2 text-xs border-2 border-gray-200 rounded-xl focus:border-indigo-500 focus:outline-none"
          ></textarea>
          <button
            @click="importBulkNames"
            class="mt-1 md:mt-1.5 w-full py-1 md:py-1.5 bg-gray-100 hover:bg-gray-200 text-gray-700 rounded-lg text-xs font-medium transition-colors flex items-center justify-center gap-1"
          >
            <svg class="w-3 h-3 md:w-3.5 md:h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12"></path>
            </svg>
            Import
          </button>
        </div>

        <!-- Name Tags - Scrollable with max height on mobile -->
        <div class="flex-1 overflow-y-auto min-h-0 max-h-40 lg:max-h-none">
          <div v-if="names.length === 0" class="text-center py-4 md:py-6 text-gray-400">
            <svg class="w-6 h-6 md:w-8 md:h-8 mx-auto mb-1 md:mb-2 text-gray-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"></path>
            </svg>
            <p class="text-xs">No names yet</p>
          </div>
          
          <div v-else class="space-y-1 md:space-y-1.5">
            <div
              v-for="name in names"
              :key="name"
              class="group relative px-2.5 md:px-3 py-1.5 md:py-2 bg-gradient-to-r from-indigo-50 to-purple-50 text-gray-700 rounded-lg text-xs font-medium border border-indigo-100 hover:shadow-md transition-all flex items-center justify-between"
            >
              <span class="truncate">{{ name }}</span>
              <button
                @click="removeName(name)"
                :disabled="isSpinning"
                class="opacity-0 group-hover:opacity-100 transition-opacity text-red-500 hover:text-red-700 font-bold ml-1 p-1"
              >
                <svg class="w-3.5 h-3.5 md:w-4 md:h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Winner Modal - Responsive -->
    <Teleport to="body">
      <Transition name="modal">
        <div
          v-if="showWinnerModal"
          class="fixed inset-0 z-50 flex items-center justify-center px-4"
          @click.self="closeModal"
        >
          <div class="absolute inset-0 bg-black/50 backdrop-blur-sm"></div>
          
          <div class="relative bg-white rounded-2xl md:rounded-3xl shadow-2xl max-w-[90%] md:max-w-md w-full p-5 md:p-8 transform transition-all">
            <div class="absolute -top-4 md:-top-5 left-1/2 -translate-x-1/2">
              <div class="w-12 h-12 md:w-16 md:h-16 bg-gradient-to-r from-yellow-400 to-orange-500 rounded-full flex items-center justify-center shadow-lg">
                <svg class="w-6 h-6 md:w-8 md:h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                </svg>
              </div>
            </div>
            
            <button
              @click="closeModal"
              class="absolute top-3 right-3 md:top-4 md:right-4 text-gray-400 hover:text-gray-600 transition-colors"
            >
              <svg class="w-5 h-5 md:w-6 md:h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
            
            <div class="text-center mt-2 md:mt-4">
              <h3 class="text-xl md:text-2xl font-bold text-gray-800 mb-2">Selected Name</h3>
              
              <div class="bg-gradient-to-r from-indigo-500 to-purple-600 rounded-xl md:rounded-2xl p-4 md:p-6 my-4 md:my-6">
                <p class="text-2xl md:text-4xl lg:text-5xl font-bold text-white break-words">
                  {{ winner }}
                </p>
              </div>
              
              <p class="text-base md:text-lg text-gray-600 mb-4 md:mb-6 flex items-center justify-center gap-2">
                <svg class="w-4 h-4 md:w-5 md:h-5 text-yellow-500" fill="currentColor" viewBox="0 0 20 20">
                  <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
                </svg>
                Time to recite!
                <svg class="w-4 h-4 md:w-5 md:h-5 text-yellow-500" fill="currentColor" viewBox="0 0 20 20">
                  <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
                </svg>
              </p>
              
              <div class="flex gap-2 md:gap-3">
                <button
                  @click="spinAgain"
                  class="flex-1 py-2 md:py-3 px-3 md:px-4 bg-gradient-to-r from-indigo-600 to-purple-600 text-white font-bold rounded-xl hover:shadow-lg transition-all flex items-center justify-center gap-1 md:gap-2 text-sm md:text-base"
                >
                  <svg class="w-4 h-4 md:w-5 md:h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                  </svg>
                  Spin Again
                </button>
                <button
                  @click="closeModal"
                  class="flex-1 py-2 md:py-3 px-3 md:px-4 bg-gray-200 hover:bg-gray-300 text-gray-700 font-bold rounded-xl transition-all text-sm md:text-base"
                >
                  Close
                </button>
              </div>
              
              <button
                @click="removeWinnerAndSpin"
                class="mt-2 md:mt-3 text-xs md:text-sm text-red-500 hover:text-red-700 transition-colors flex items-center justify-center gap-1 mx-auto"
              >
                <svg class="w-3.5 h-3.5 md:w-4 md:h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
                </svg>
                Remove & Spin Again
              </button>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>

    <!-- Confirmation Modal - Responsive -->
    <Teleport to="body">
      <Transition name="modal">
        <div
          v-if="showConfirmModal"
          class="fixed inset-0 z-50 flex items-center justify-center px-4"
          @click.self="showConfirmModal = false"
        >
          <div class="absolute inset-0 bg-black/50 backdrop-blur-sm"></div>
          
          <div class="relative bg-white rounded-2xl shadow-2xl max-w-[90%] md:max-w-sm w-full p-5 md:p-6">
            <div class="text-center">
              <div class="w-12 h-12 md:w-14 md:h-14 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-3 md:mb-4">
                <svg class="w-6 h-6 md:w-7 md:h-7 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path>
                </svg>
              </div>
              
              <h3 class="text-base md:text-lg font-bold text-gray-800 mb-2">{{ confirmTitle }}</h3>
              <p class="text-xs md:text-sm text-gray-600 mb-5 md:mb-6">{{ confirmMessage }}</p>
              
              <div class="flex gap-2 md:gap-3">
                <button
                  @click="handleConfirm"
                  class="flex-1 py-2 md:py-2.5 bg-red-600 hover:bg-red-700 text-white font-semibold rounded-lg transition-colors text-xs md:text-sm"
                >
                  {{ confirmButtonText }}
                </button>
                <button
                  @click="showConfirmModal = false"
                  class="flex-1 py-2 md:py-2.5 bg-gray-200 hover:bg-gray-300 text-gray-700 font-semibold rounded-lg transition-colors text-xs md:text-sm"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, nextTick, onUnmounted } from 'vue'

// Pool of random names for initial load
const NAME_POOL = [
  "Emma Johnson", "Liam Smith", "Olivia Brown", "Noah Davis", 
  "Ava Wilson", "Ethan Martinez", "Sophia Anderson", "Mason Taylor",
  "Isabella Thomas", "Lucas Moore", "Mia Jackson", "Elijah White",
  "Charlotte Harris", "James Martin", "Amelia Thompson", "Benjamin Garcia",
  "Harper Rodriguez", "Alexander Lewis", "Evelyn Lee", "Daniel Walker",
  "Abigail Hall", "Matthew Allen", "Emily Young", "David King",
  "Elizabeth Wright", "Joseph Scott", "Sofia Green", "Samuel Adams"
]

// Generate 8 random unique names from pool
const getRandomDefaultNames = () => {
  const shuffled = [...NAME_POOL].sort(() => Math.random() - 0.5)
  return shuffled.slice(0, 8)
}

// Generate a unique session ID for this browser/device
const generateSessionId = () => {
  let sessionId = localStorage.getItem('wheel_session_id')
  if (!sessionId) {
    sessionId = 'session_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9)
    localStorage.setItem('wheel_session_id', sessionId)
  }
  return sessionId
}

const sessionId = generateSessionId()

// Reactive state
const names = ref(getRandomDefaultNames())
const newName = ref('')
const bulkNames = ref('')
const showBulkImport = ref(false)
const isSpinning = ref(false)
const winner = ref('')
const wheelCanvas = ref(null)
const canvasContainer = ref(null)
const rotation = ref(0)
const showWinnerModal = ref(false)
const soundEnabled = ref(true)

// Canvas responsive size
const canvasSize = ref(500)

// Confirmation modal state
const showConfirmModal = ref(false)
const confirmTitle = ref('')
const confirmMessage = ref('')
const confirmButtonText = ref('')
const confirmAction = ref(null)

// Audio context and sounds
let audioContext = null
let spinSoundBuffer = null
let winSoundBuffer = null
let tickSoundBuffer = null
let tickInterval = null
let animationFrame = null
let resizeObserver = null

// Colors for wheel segments
const colors = [
  '#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FFEAA7',
  '#DDA0DD', '#98D8C8', '#F7DC6F', '#BB8FCE', '#85C1E2',
  '#F8B195', '#F67280', '#C06C84', '#6C5B7B', '#355C7D'
]

// Generate random spin duration between 4 and 10 seconds
const getRandomSpinDuration = () => {
  return (4 + Math.random() * 6) * 1000
}

// Calculate canvas size based on container
const updateCanvasSize = () => {
  if (!canvasContainer.value) return
  
  const containerWidth = canvasContainer.value.clientWidth
  // Max size 650px, min 300px, otherwise use container width
  const size = Math.min(650, Math.max(300, containerWidth))
  canvasSize.value = size
  
  nextTick(() => drawWheel())
}

// Initialize audio
const initAudio = async () => {
  if (audioContext) return
  
  try {
    audioContext = new (window.AudioContext || window.webkitAudioContext)()
    spinSoundBuffer = await createSpinSound()
    winSoundBuffer = await createWinSound()
    tickSoundBuffer = await createTickSound()
  } catch (e) {
    console.warn('Audio initialization failed:', e)
  }
}

// Create spin sound
const createSpinSound = async () => {
  const sampleRate = audioContext.sampleRate
  const duration = 0.05
  const samples = duration * sampleRate
  const buffer = audioContext.createBuffer(1, samples, sampleRate)
  const data = buffer.getChannelData(0)
  
  for (let i = 0; i < samples; i++) {
    const t = i / sampleRate
    const envelope = Math.exp(-t * 30)
    const freq = 800 + Math.random() * 400
    data[i] = envelope * Math.sin(2 * Math.PI * freq * t) * 0.3
  }
  
  return buffer
}

// Create win sound
const createWinSound = async () => {
  const sampleRate = audioContext.sampleRate
  const duration = 1.5
  const samples = duration * sampleRate
  const buffer = audioContext.createBuffer(1, samples, sampleRate)
  const data = buffer.getChannelData(0)
  
  const notes = [523.25, 659.25, 783.99, 1046.50]
  
  for (let i = 0; i < samples; i++) {
    const t = i / sampleRate
    let value = 0
    
    notes.forEach((freq, index) => {
      const startTime = index * 0.15
      if (t >= startTime) {
        const noteTime = t - startTime
        const envelope = Math.exp(-noteTime * 3)
        value += envelope * Math.sin(2 * Math.PI * freq * t) * 0.15
      }
    })
    
    data[i] = value
  }
  
  return buffer
}

// Create tick sound
const createTickSound = async () => {
  const sampleRate = audioContext.sampleRate
  const duration = 0.02
  const samples = duration * sampleRate
  const buffer = audioContext.createBuffer(1, samples, sampleRate)
  const data = buffer.getChannelData(0)
  
  for (let i = 0; i < samples; i++) {
    const t = i / sampleRate
    const envelope = Math.exp(-t * 100)
    data[i] = envelope * Math.sin(2 * Math.PI * 1200 * t) * 0.2
  }
  
  return buffer
}

// Play a sound buffer
const playSound = (buffer, volume = 1.0) => {
  if (!soundEnabled.value || !audioContext || !buffer) return
  
  if (audioContext.state === 'suspended') {
    audioContext.resume()
  }
  
  const source = audioContext.createBufferSource()
  source.buffer = buffer
  
  const gainNode = audioContext.createGain()
  gainNode.gain.value = volume
  
  source.connect(gainNode)
  gainNode.connect(audioContext.destination)
  
  source.start()
  return source
}

// Play spin tick sound
const playTickSound = () => {
  if (!soundEnabled.value) return
  playSound(tickSoundBuffer, 0.3)
}

// Start spin sound
const startSpinSound = () => {
  if (!soundEnabled.value) return
  
  playSound(spinSoundBuffer, 0.4)
  
  tickInterval = setInterval(() => {
    if (isSpinning.value) {
      playTickSound()
    }
  }, 80)
}

// Stop spin sound
const stopSpinSound = () => {
  if (tickInterval) {
    clearInterval(tickInterval)
    tickInterval = null
  }
}

// Play winner sound
const playWinSound = () => {
  if (!soundEnabled.value) return
  playSound(winSoundBuffer, 0.5)
}

// Get storage key for this session
const getStorageKey = () => {
  return `nameWheel_${sessionId}`
}

// Load from localStorage (isolated by session)
onMounted(() => {
  const storageKey = getStorageKey()
  const saved = localStorage.getItem(storageKey)
  
  if (saved) {
    try {
      const data = JSON.parse(saved)
      if (data.names && data.names.length > 0) {
        names.value = data.names
      } else {
        names.value = getRandomDefaultNames()
      }
      soundEnabled.value = data.soundEnabled !== undefined ? data.soundEnabled : true
    } catch (e) {
      console.error('Failed to load saved data')
      names.value = getRandomDefaultNames()
    }
  }
  
  // Set up resize observer
  if (canvasContainer.value) {
    resizeObserver = new ResizeObserver(() => updateCanvasSize())
    resizeObserver.observe(canvasContainer.value)
    updateCanvasSize()
  }
  
  document.addEventListener('click', initAudio, { once: true })
})

// Clean up
onUnmounted(() => {
  stopSpinSound()
  if (animationFrame) {
    cancelAnimationFrame(animationFrame)
  }
  if (audioContext) {
    audioContext.close()
  }
  if (resizeObserver) {
    resizeObserver.disconnect()
  }
})

// Save to localStorage (isolated by session)
watch([names, soundEnabled], () => {
  const storageKey = getStorageKey()
  localStorage.setItem(storageKey, JSON.stringify({
    names: names.value,
    soundEnabled: soundEnabled.value,
    lastUpdated: new Date().toISOString()
  }))
}, { deep: true })

// Draw the wheel
const drawWheel = () => {
  if (!wheelCanvas.value) return
  
  const canvas = wheelCanvas.value
  const ctx = canvas.getContext('2d')
  const centerX = canvas.width / 2
  const centerY = canvas.height / 2
  const radius = Math.min(centerX, centerY) - Math.max(20, canvas.width * 0.04)
  
  ctx.clearRect(0, 0, canvas.width, canvas.height)
  
  if (names.value.length === 0) return
  
  const angleStep = (2 * Math.PI) / names.value.length
  
  names.value.forEach((name, index) => {
    const startAngle = index * angleStep + rotation.value
    const endAngle = startAngle + angleStep
    
    ctx.beginPath()
    ctx.moveTo(centerX, centerY)
    ctx.arc(centerX, centerY, radius, startAngle, endAngle)
    ctx.closePath()
    
    ctx.fillStyle = colors[index % colors.length]
    ctx.fill()
    
    ctx.strokeStyle = '#fff'
    ctx.lineWidth = Math.max(1.5, canvas.width * 0.003)
    ctx.stroke()
    
    ctx.save()
    ctx.translate(centerX, centerY)
    ctx.rotate(startAngle + angleStep / 2)
    ctx.textAlign = 'center'
    ctx.textBaseline = 'middle'
    ctx.fillStyle = '#1a1a1a'
    const fontSize = Math.max(12, canvas.width * 0.022)
    ctx.font = `bold ${fontSize}px "Segoe UI", system-ui, sans-serif`
    
    let displayName = name
    if (displayName.length > 14) {
      displayName = displayName.substring(0, 12) + '..'
    }
    
    ctx.fillText(displayName, radius * 0.65, 0)
    ctx.restore()
  })
}

// Spin the wheel with natural physics
const spinWheel = async () => {
  if (isSpinning.value || names.value.length === 0) {
    if (names.value.length === 0) {
      showConfirmModal.value = true
      confirmTitle.value = 'No Names'
      confirmMessage.value = 'Please add at least one name to the wheel first.'
      confirmButtonText.value = 'Got it'
      confirmAction.value = () => { showConfirmModal.value = false }
    }
    return
  }
  
  await initAudio()
  
  isSpinning.value = true
  winner.value = ''
  
  startSpinSound()
  
  const spinDuration = getRandomSpinDuration()
  const startTime = Date.now()
  const startRotation = rotation.value
  
  // Random number of full rotations (8-15)
  const totalRotations = 8 + Math.random() * 7
  const targetRotation = startRotation + (totalRotations * 2 * Math.PI)
  
  const animate = () => {
    const elapsed = Date.now() - startTime
    const progress = Math.min(elapsed / spinDuration, 1)
    
    // Natural easing: easeOutCubic for smooth deceleration
    const easeOut = 1 - Math.pow(1 - progress, 3)
    
    rotation.value = startRotation + (targetRotation - startRotation) * easeOut
    drawWheel()
    
    // Slow down tick sound as wheel slows
    if (tickInterval && progress > 0.6) {
      clearInterval(tickInterval)
      const newInterval = Math.floor(80 + (progress - 0.6) * 200)
      tickInterval = setInterval(() => {
        if (isSpinning.value) {
          playTickSound()
        }
      }, newInterval)
    }
    
    if (progress < 1) {
      animationFrame = requestAnimationFrame(animate)
    } else {
      isSpinning.value = false
      stopSpinSound()
      animationFrame = null
      
      if (names.value.length > 0) {
        determineWinner()
      }
    }
  }
  
  animationFrame = requestAnimationFrame(animate)
}

// Determine winner - can stop at ANY position in the segment
const determineWinner = () => {
  if (names.value.length === 0) return
  
  const angleStep = (2 * Math.PI) / names.value.length
  const pointerAngle = -Math.PI / 2
  
  let normalizedRotation = rotation.value % (2 * Math.PI)
  if (normalizedRotation < 0) normalizedRotation += 2 * Math.PI
  
  for (let i = 0; i < names.value.length; i++) {
    const startAngle = i * angleStep + normalizedRotation
    let endAngle = startAngle + angleStep
    
    let start = startAngle % (2 * Math.PI)
    if (start < 0) start += 2 * Math.PI
    
    let end = endAngle % (2 * Math.PI)
    if (end < 0) end += 2 * Math.PI
    
    let pointer = pointerAngle
    if (pointer < 0) pointer += 2 * Math.PI
    
    if (start < end) {
      if (pointer >= start && pointer < end) {
        setWinner(names.value[i])
        return
      }
    } else {
      if (pointer >= start || pointer < end) {
        setWinner(names.value[i])
        return
      }
    }
  }
}

const setWinner = (name) => {
  winner.value = name
  playWinSound()
  showWinnerModal.value = true
}

// Modal controls
const closeModal = () => {
  showWinnerModal.value = false
}

const spinAgain = () => {
  closeModal()
  setTimeout(() => spinWheel(), 300)
}

const removeWinnerAndSpin = () => {
  if (winner.value) {
    names.value = names.value.filter(n => n !== winner.value)
    closeModal()
    setTimeout(() => {
      if (names.value.length > 0) {
        nextTick(() => drawWheel())
        spinWheel()
      } else {
        drawWheel()
      }
    }, 300)
  }
}

// Confirmation dialog for clearing all names
const confirmClearAll = () => {
  if (names.value.length === 0) {
    showConfirmModal.value = true
    confirmTitle.value = 'No Names'
    confirmMessage.value = 'There are no names to delete.'
    confirmButtonText.value = 'OK'
    confirmAction.value = () => { showConfirmModal.value = false }
    return
  }
  
  showConfirmModal.value = true
  confirmTitle.value = 'Delete All Names'
  confirmMessage.value = `Are you sure you want to delete all ${names.value.length} names from the wheel?`
  confirmButtonText.value = 'Delete All'
  confirmAction.value = () => {
    names.value = []
    winner.value = ''
    rotation.value = 0
    showConfirmModal.value = false
    nextTick(() => drawWheel())
  }
}

const handleConfirm = () => {
  if (confirmAction.value) {
    confirmAction.value()
  }
  showConfirmModal.value = false
}

// Shuffle to new random 8 names
const shuffleDefaultNames = () => {
  names.value = getRandomDefaultNames()
  winner.value = ''
  rotation.value = rotation.value + Math.random() * 2 * Math.PI
  nextTick(() => drawWheel())
}

// Name management
const addName = () => {
  const name = newName.value.trim()
  if (name && !names.value.includes(name)) {
    names.value.push(name)
    newName.value = ''
    nextTick(() => drawWheel())
  } else if (names.value.includes(name)) {
    showConfirmModal.value = true
    confirmTitle.value = 'Duplicate Name'
    confirmMessage.value = `${name} is already in the wheel.`
    confirmButtonText.value = 'OK'
    confirmAction.value = () => { showConfirmModal.value = false }
    newName.value = ''
  }
}

const removeName = (name) => {
  names.value = names.value.filter(n => n !== name)
  nextTick(() => drawWheel())
}

const importBulkNames = () => {
  const importedNames = bulkNames.value
    .split(',')
    .map(n => n.trim())
    .filter(n => n && !names.value.includes(n))
  
  if (importedNames.length > 0) {
    names.value.push(...importedNames)
    bulkNames.value = ''
    showBulkImport.value = false
    nextTick(() => drawWheel())
  }
}
</script>

<style scoped>
/* Add xs breakpoint for very small devices */
@media (min-width: 480px) {
  .xs\:inline {
    display: inline;
  }
}

.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-active .relative,
.modal-leave-active .relative {
  transition: transform 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .relative,
.modal-leave-to .relative {
  transform: scale(0.9) translateY(20px);
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.8; }
}

.animate-pulse {
  animation: pulse 1s ease-in-out infinite;
}
</style>