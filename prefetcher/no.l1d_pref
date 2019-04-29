#include "cache.h"
#define N 64

class STATS {
  public: 
	  uint32_t total_accesses;
	  uint32_t page_changes;
	  uint32_t last_n_pages;

	  uint64_t last_n[N];
	  uint32_t lru[N];
	  
	  uint64_t last_page;
	  uint32_t last_1_count;

	  STATS() {
		  total_accesses = 0;
		  page_changes = 0;
	  	  last_n_pages = 0;
	  	  last_page = 0;
	  	  last_1_count = 0;
	  	  for(int i=0; i<N; i++) {
			  last_n[i] = 0;
			  lru[i] = i;
	  	  }
	}
};

STATS STAT;

void CACHE::l1d_prefetcher_initialize() 
{

}

void CACHE::l1d_prefetcher_operate(uint64_t addr, uint64_t ip, uint8_t cache_hit, uint8_t type)
{
	uint64_t curr_page = addr >> LOG2_PAGE_SIZE;
	STAT.total_accesses++;

	bool matched = 0;
	int way = N;
	for (int i=0; i<N; i++) { // Looking for a match
		if (curr_page == STAT.last_n[i]) {// Match found in last N pages
			matched = 1;
			way = i; // Saving the way
			STAT.last_n_pages++;
		}
	} 
	if (!matched) {// Match not found
		STAT.page_changes++;
	}

	if(matched) { // If it was a match
		for (int j=0; j<N; j++) { // Iterating pages
			if (STAT.lru[j] < STAT.lru[way]) { // If given lru < match lru
				STAT.lru[j]++; // increment given lru
			}
		}
		STAT.lru[way] = 0; // match lru = 0
	} else { // If no match
		for (int j=0; j<N; j++) { // Iterate
			if (STAT.lru[j] == N-1) { // Find victim
				STAT.last_n[j] = curr_page; // Replace victim
				STAT.lru[j] = 0; // victim lru = 0
			} else {
				STAT.lru[j]++; // Demote other pages
			}
		}
	}

	if (curr_page == STAT.last_page)
		STAT.last_1_count++;
	STAT.last_page = curr_page;

}

void CACHE::l1d_prefetcher_cache_fill(uint64_t addr, uint32_t set, uint32_t way, uint8_t prefetch, uint64_t evicted_addr, uint32_t metadata_in)
{

}

void CACHE::l1d_prefetcher_final_stats()
{
	cout << "Total Accesses: " << STAT.total_accesses << endl;
	cout << "Last N Pages: " << STAT.last_n_pages << endl;
	cout << "Page Changes: " << STAT.page_changes << endl;
	cout << "Last 1 Count: " << STAT.last_1_count << endl;

}
