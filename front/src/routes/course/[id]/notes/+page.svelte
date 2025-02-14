<script lang="ts">
  import { page } from '$app/stores';
  import rq from '$lib/rq/rq.svelte';
  import type { components } from '$lib/types/api/v1/schema';

  let notes: components['schemas']['SummaryNoteDto'][] = $state();

  async function load() {
    if (import.meta.env.SSR) throw new Error('CSR ONLY');

    const { data } = await rq.apiEndPoints().GET(`/api/v1/courses/summary/{writerId}/{courseId}`, {
      params: {
        path: {
          writerId: rq.member.id,
          courseId: parseInt($page.params.id)
        }
      }
    });

    notes = data!.data;

    return { notes };
  }
</script>

{#await load()}
  <p class="text-center">loading...</p>
{:then { notes }}
  <div class="flex">
    <div class="hidden w-64 border-r bg-gray-100/40 lg:block dark:bg-gray-800/40">
      <div class="flex flex-col gap-2">
        <div class="flex items-center h-16 px-4 border-b border-gray-200 dark:border-gray-800">
          <a class="flex items-center gap-2 font-semibold" href="/"
            ><svg
              xmlns="http://www.w3.org/2000/svg"
              width="24"
              height="24"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
              class="h-6 w-6"
            >
              <path d="m8 3 4 8 5-5 5 15H2L8 3z"></path></svg
            ><span class="">EduBridge</span></a
          >
        </div>
        <ul class="menu w-56 rounded-box">
          <li>
            <a
              class="flex items-center gap-3 rounded-lg px-3 py-2 text-sm font-bold transition-colors hover:bg-gray-100 hover:text-gray-900 dark:hover:bg-gray-800 dark:hover:text-gray-50"
              href="/member/course"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="feather feather-home"
                ><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" /><polyline
                  points="9 22 9 12 15 12 15 22"
                /></svg
              >

              내 강의실
            </a>
          </li>
          <li>
            <a
              class="flex items-center gap-3 rounded-lg px-3 py-2 text-sm font-bold transition-colors hover:bg-gray-100 hover:text-gray-900 dark:hover:bg-gray-800 dark:hover:text-gray-50"
              href="/course/{parseInt($page.params.id)}/notes"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="feather feather-edit"
                ><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7" /><path
                  d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"
                /></svg
              >

              요약 노트
            </a>
          </li>
          <li>
            <a
              class="flex items-center gap-3 rounded-lg px-3 py-2 text-sm font-bold transition-colors hover:bg-gray-100 hover:text-gray-900 dark:hover:bg-gray-800 dark:hover:text-gray-50"
              href="/course/{parseInt($page.params.id)}"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="w-6 h-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M9 15 3 9m0 0 6-6M3 9h12a6 6 0 0 1 0 12h-3"
                />
              </svg>

              강좌 돌아가기
            </a>
          </li>
        </ul>
      </div>
    </div>
    <div class="py-8 px-56 w-full">
      <div>
        {#if notes && notes.length > 0}
          <div class="mb-5">
            <div class="mt-3 space-y-4">
              {#each notes as item, index}
                <div class="flex justify-col justify-end">
                  <div>
                    <a href="/course/{item.courseId}">
                      <h2 class="text-2xl font-semibold text-blue-600 hover:text-blue-900">
                        {item.courseName}
                      </h2>
                    </a>
                  </div>
                </div>
                <div class="bg-white shadow overflow-hidden rounded-lg">
                  <div class="px-4 py-5 sm:p-6">
                    <div class="flex items-center justify-between">
                      <div class="text-sm font-medium text-gray-900">
                        <a
                          href="/course/{item.courseId}/{item.videoId}/summary/{item.id}"
                          class="text-xl text-blue-600 hover:text-blue-900"
                        >
                          요약 노트 {index + 1}
                        </a>
                      </div>
                      <div class="ml-2 flex-shrink-0 flex">
                        <p class="text-sm text-gray-500 mr-2">점수: {item.score}</p>
                        <p
                          class={`inline-flex px-2 text-xs leading-5 font-semibold rounded-full ${!item.pass ? 'bg-red-100 text-red-800' : 'bg-green-100 text-green-800'}`}
                        >
                          {item.pass ? 'Pass' : 'Fail'}
                        </p>
                      </div>
                    </div>
                    <div class="mt-2">
                      <p class="text-sm text-gray-500">
                        작성일: {`${new Date(item.createDate).getFullYear()}년 ${new Date(item.createDate).getMonth() + 1}월 ${new Date(item.createDate).getDate()}일`}
                      </p>
                    </div>
                  </div>
                </div>
              {/each}
            </div>
          </div>
        {:else}
          <p>요약 노트가 없습니다.</p>
        {/if}
      </div>
    </div>
  </div>
{/await}
