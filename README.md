# Tempelate_Project_Keil_FreeRTOS
Keil v.5 и v.6
1.	Качаем freertos.org и распакуем FreeRTOSv202406.01-LTS (на момент 10.2025г)
2.	Если нет существующего проекта, создадим:
CMSIS
<img width="1226" height="950" alt="2025-10-16_07-52-37" src="https://github.com/user-attachments/assets/978e2b5f-1c94-4d3c-bb92-7217c5c6ecfc" />
Или HAL
<img width="1225" height="953" alt="2025-10-16_07-53-36" src="https://github.com/user-attachments/assets/0c0afcd9-5205-49df-8c57-e347479ceeca" />

3.	В распакованном FreeRTOS находим FreeRTOS-Kernel (остальное для Keil не нужно), копируем в корень своего проекта (можно изменить имя, как удобно…)
<img width="668" height="429" alt="2" src="https://github.com/user-attachments/assets/2f6583da-074f-4e89-89d2-c0d7e493e8ae" />
<img width="505" height="323" alt="2025-10-16_07-59-50" src="https://github.com/user-attachments/assets/f030f156-ea93-4f9e-9086-0176a5ac5c90" />

4.	Директория FreeRTOS\FreeRTOS-Kernel\examples\template_configuration содержит пустой FreeRTOSConfig, его надо настроить под свой контроллер
<img width="416" height="195" alt="2025-10-16_08-35-50" src="https://github.com/user-attachments/assets/c93d3af8-fec7-4a9b-9978-e7e6014a703d" />

Пример: Для STM32F4x выглядит так:
#ifndef FREERTOS_CONFIG_H
#define FREERTOS_CONFIG_H

#include <stdint.h>
#endif

extern uint32_t SystemCoreClock;

#define configUSE_PREEMPTION			1
#define configUSE_IDLE_HOOK				0
#define configUSE_TICK_HOOK				0
#define configCPU_CLOCK_HZ				( SystemCoreClock )
#define configTICK_RATE_HZ				( ( portTickType ) 1000)
#define configMAX_PRIORITIES			(  5 )
#define configMINIMAL_STACK_SIZE		( ( unsigned short ) 130 )
#define configTOTAL_HEAP_SIZE			( ( size_t ) ( 40960 ) )
#define configMAX_TASK_NAME_LEN			( 10 )
#define configUSE_TRACE_FACILITY		1
#define configUSE_16_BIT_TICKS			0
#define configIDLE_SHOULD_YIELD			1
#define configUSE_MUTEXES				1
#define configQUEUE_REGISTRY_SIZE		8
#define configCHECK_FOR_STACK_OVERFLOW	0
#define configUSE_RECURSIVE_MUTEXES		1
#define configUSE_MALLOC_FAILED_HOOK	1
#define configUSE_APPLICATION_TASK_TAG	0
#define configUSE_COUNTING_SEMAPHORES	1

/* Co-routine definitions. */
#define configUSE_CO_ROUTINES 			0
#define configMAX_CO_ROUTINE_PRIORITIES ( 2 )

/* Software timer definitions. */
#define configUSE_TIMERS				1
#define configTIMER_TASK_PRIORITY		( configMAX_PRIORITIES - 1 )
#define configTIMER_QUEUE_LENGTH		5
#define configTIMER_TASK_STACK_DEPTH	( configMINIMAL_STACK_SIZE * 2 )

/* Set the following definitions to 1 to include the API function, or zero
to exclude the API function. */
#define INCLUDE_vTaskPrioritySet		1
#define INCLUDE_uxTaskPriorityGet		1
#define INCLUDE_vTaskDelete				1
#define INCLUDE_vTaskCleanUpResources	1
#define INCLUDE_vTaskSuspend			1
#define INCLUDE_vTaskDelayUntil			1
#define INCLUDE_vTaskDelay				1

/* FreeRTOS+CLI definitions. */

/* Dimensions a buffer into which command outputs can be written.  The buffer
can be declared in the CLI code itself, to allow multiple command consoles to
share the same buffer.  For example, an application may allow access to the
command interpreter by UART and by Ethernet.  Sharing a buffer is done purely
to save RAM.  Note, however, that the command console itself is not re-entrant,
so only one command interpreter interface can be used at any one time.  For
that reason, no attempt at providing mutual exclusion to the buffer is
attempted. */
#define configCOMMAND_INT_MAX_OUTPUT_SIZE 400


/* Cortex-M specific definitions. */

#ifdef __NVIC_PRIO_BITS
	/* __BVIC_PRIO_BITS will be specified when CMSIS is being used. */
	#define configPRIO_BITS       		__NVIC_PRIO_BITS
#else
	#define configPRIO_BITS       		4        /* 15 priority levels */
#endif

/* The lowest interrupt priority that can be used in a call to a "set priority"
function. */
#define configLIBRARY_LOWEST_INTERRUPT_PRIORITY			0x0f

/* The highest interrupt priority that can be used by any interrupt service
routine that makes calls to interrupt safe FreeRTOS API functions.  DO NOT CALL
INTERRUPT SAFE FREERTOS API FUNCTIONS FROM ANY INTERRUPT THAT HAS A HIGHER
PRIORITY THAN THIS! (higher priorities are lower numeric values. */
#define configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY	10

/* Interrupt priorities used by the kernel port layer itself.  These are generic
to all Cortex-M ports, and do not rely on any particular library functions. */
#define configKERNEL_INTERRUPT_PRIORITY 		( configLIBRARY_LOWEST_INTERRUPT_PRIORITY << (8 - configPRIO_BITS) )
#define configMAX_SYSCALL_INTERRUPT_PRIORITY 	( configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY << (8 - configPRIO_BITS) )

/* Normal assert() semantics without relying on the provision of an assert.h
header file. */
#define configASSERT( x ) if( ( x ) == 0 ) { taskDISABLE_INTERRUPTS(); for( ;; ) __asm volatile( "NOP" ); }
#define INCLUDE_MODULE_TEST 0
#endif /* FREERTOS_CONFIG_H */

и после FreeRTOSConfig.h сохранить в \FreeRTOS-Kernel\include (который в корне проекта)
<img width="416" height="195" alt="2025-10-16_08-35-50" src="https://github.com/user-attachments/assets/7475cd14-bae9-4cdb-b906-9e697278847f" />

6.	Создаем в корне проекта директорию и называем ее RTOS, подключаем к ней Си файлы из FreeRTOS-Kernel
<img width="1375" height="792" alt="2025-10-16_08-49-08" src="https://github.com/user-attachments/assets/cfaf1df3-4f0e-453d-b9fe-7906032dae78" />
А так же из директории \FreeRTOS-Kernel\portable, и тут ВАЖНО, если Keil с версии компилятора 5 то идем в директорию \FreeRTOS-Kernel\portable\RVDS\ARM_CM4F (для STM32F4x), а если Keil с версии компилятора 6 то идем в директорию \FreeRTOS-Kernel\portable\GCC\ARM_CM4F (для STM32F4x)
<img width="996" height="971" alt="2025-10-16_08-54-33" src="https://github.com/user-attachments/assets/2c6f85d3-fa67-4079-b7c3-2134bd2fdfc5" />
В GCC или RVDS выбираем нужный микроконтроллер (в моем случае для STM32F4x это директория ARM_CM4F) и подключаем port.c
После заходим директорию \FreeRTOS-Kernel\portable\MemMang и выбираем си файл (в моем случае для STM32F4x это heap_4.c)
<img width="982" height="956" alt="2025-10-16_08-59-30" src="https://github.com/user-attachments/assets/ad15775e-4e63-440a-a8b1-1244e55a523c" />
Должно в итоге получится так:
<img width="287" height="550" alt="2025-10-16_09-00-30" src="https://github.com/user-attachments/assets/13a79947-909b-4505-9359-f899d85223e3" />

7.	В проекте Keil подключить указать пути include для RTOS
<img width="786" height="588" alt="2025-10-16_09-03-43" src="https://github.com/user-attachments/assets/572d0655-029c-45ee-b14e-4e393c2ac180" />

8.	Осталось настроить точку входа, как минимум:
<img width="876" height="885" alt="2025-10-16_09-08-35" src="https://github.com/user-attachments/assets/49989e1f-8197-48d8-99af-e00e0393fa1d" />
<img width="741" height="905" alt="2025-10-16_09-09-56" src="https://github.com/user-attachments/assets/555d42c2-433e-4494-9294-495be693520e" />
Особо важно прописать реализацию прототипов функций-обработчиков FreeRTOS, пусть функции пустые, но быть должны..
<img width="889" height="880" alt="2025-10-16_09-15-07" src="https://github.com/user-attachments/assets/806750d9-0df7-49af-b73a-eb797a954c3b" />

9.	Собираем проект!
<img width="766" height="168" alt="2025-10-16_09-16-33" src="https://github.com/user-attachments/assets/9409d205-6ccf-4c01-b77d-cf4551af9f3c" />








