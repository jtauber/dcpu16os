; simple operating system for the DCPU-16

; until we have something like EQU, we'll just document memory locations here

; vidmem    = 0x8000
; cursor    = 0x1000
; termwidth = 0x1001
; termheight = 0x1002

SET PC, start


;; utilities

:mem_copy                           ; copy [X:X+Z+1] -> [Y:Y+Z+1]
    SET I, 0
:mem_copy_loop
    IFE I, Z
        SET PC, POP ; return
    SET [Y], [X]
    ADD X, 1
    ADD Y, 1
    ADD I, 1
    SET PC, mem_copy_loop


;; video

:clear_screen
    SET I, 0x8000
    SET Z, [0x1001]
    MUL Z, [0x1002]
    ADD Z, 0x8000                       ; z = (termwidth * termheight) + vidmem
:clear_screen_loop                      ; loop i through video memory
    SET [I], 0x20                       ;    clearing screen
    ADD I, 1
    IFE I, Z
        SET PC, clear_screen_done
    SET PC, clear_screen_loop
:clear_screen_done
    SET [0x1000], 0                ; set cursor to top left
    SET PC, POP ; return

:println
    JSR print
    JSR newline
    SET PC, POP ; return

:print                              ; prints string starting at X
    SET I, X
:print_loop                             ; loop through string at A
    IFE [I], 0                          ; if hit NULL
        SET PC, POP                     ;     return
    SET A, [I]
    JSR print_char
    ADD I, 1
    SET PC, print_loop

; doesn't scroll when at end yet
:print_char                         ; print character in A
    SET Y, [0x1000]                     ; set Y to cursor
    SET [0x8000 + Y], A
    ADD [0x1000], 1                     ; move cursor
    SET PC, POP                         ; return

:newline                            ; clears to end of line
    SET Y, [0x1000]
    MOD Y, [0x1001]                     ; y = cursor 
    IFE Y, 0
        SET PC, POP
    SET [0x8000 + Y], 0x20
    ADD [0x1000], 1
    SET PC, newline

:scroll
    SET J, 1
    SET Z, [0x1001]
    :scroll_loop
    IFE J, [0x1002]
        SET PC, POP ; return
    SET I, J
    MUL I, [0x1001]
    SET X, 0x8000
    ADD X, I
    SET Y, X
    SUB Y, [0x1001]
    JSR mem_copy
    ADD J, 1
    SET PC, scroll_loop


;; start

:banner DAT "DCPU-16 Operating System", 0

:start
    SET [0x1001], 80                    ; terminal width
    SET [0x1002], 24                    ; terminal height
    JSR clear_screen
    SET X, banner
    JSR println

:halt
    SET PC, halt
