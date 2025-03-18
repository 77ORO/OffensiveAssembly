# Windows Sleep function

> Calls the windows Sleep function by iterating over all functions that are inside kernel32.dll
> MS documentation: https://learn.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-sleep

Save Registers

	50						push rax
	53 						push rbx
	51 						push rcx
	52 						push rdx
	56 						push rsi
	57 						push rdi

Get kernel32.dll base address and entry to functions

	BA60000000 				mov rdx,0x60
	65488B32 				mov rsi,[gs:rdx]
	488B7618 				mov rsi,[rsi+0x18]
	488B7610 				mov rsi,[rsi+0x10]
	48AD 					lodsq
	488B30 					mov rsi,[rax]
	488B7E30 				mov rdi,[rsi+0x30]
	03573C 					add edx,[rdi+0x3c]
	8B5C1728 				mov ebx,[rdi+rdx+0x28]
	8B741F20 				mov esi,[rdi+rbx+0x20]
	4801FE 					add rsi,rdi
	8B541F24 				mov edx,[rdi+rbx+0x24]

Iterate through all function names to find correct function by name

	0FB72C17 				movzx ebp,word [rdi+rdx]
	8D5202 					lea edx,[rdx+0x2]
	AD 						lodsd
	813C07536C6565 			cmp dword [rdi+rax],0x65656C53; eelS (Sleep Search: Search for "Slee")
	75EF 					jnz 0x3f

Get function call location

	8B741F1C 				mov esi,[rdi+rbx+0x1c]
	4801FE 					add rsi,rdi
	8B34AE 					mov esi,[rsi+rbp*4]
	4801F7 					add rdi,rsi

Define Sleep in miliseconds

	B9603A0000 				mov ecx,0xEA60   ; 60'000 miliseconds to sleep // mov ecx,60000

Call Sleep

	FFD7 					call rdi

Restore registers

	5F 						pop rdi
	5E 						pop rsi
	5A 						pop rdx
	59 						pop rcx
	5B 						pop rbx
	58 						pop rax

