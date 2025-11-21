# zorton_reverse
Intento de hacer un poco de ingenieria inversa de el arcade de Zorton Brothers.

# video dump
el vídeo ripeado por los grandes de Recreativas.org  puedes encontrarlo aquí:
https://archive.org/details/los-justicieros-zorton-brothers-picmatic-1993-cav-pal-spanish


# Visuazlicer
en el directorio ./visualizer está el programa hecho por "@logan76able" para ver los frames y rangosd e frames del vídeo.
para ejecturarlo:

```bash
cd visualizer
uv run python main.py

```
Ten encuenta que el vídeo deberás tenerlo descargado previamente.



# ghidra
en esta version estamos usando ghidra 10.3.1. con el plugin para desensamblar Amiga500 y cargar el formato ejecutable de Amiga Hunk.
https://github.com/lab313ru/ghidra_amiga_ldr



# test_python
en este directorio estamos guardando los scripts que estamos desarrollando para extraer la estructura de los datos.

## formato del JSON

"scene_order": Listado de escenas, se empieza desde el 0 y la escena final la 25. Despus de eso creo que todo son muertes distintas y otras cosas que tenemos que ver que son.  Pero con esto se sabe que de la escena en 0 se salta a la 1, de ahi a la 2, and so on.

"chunks": listado de los chunks por escena. cada chunch tiene su frame inicial, frame final y hitboxes, cuando puedes disparar a cada hitbox y en secuences siempre es 0-> falla el disparo o se acaba el time,1 -> se le da al hitbox 1, 2->se le da al htibox 2, etc... 
Tambien tiene un "ptr_node_respawn" que es el inicio y fin cuando te matan en esta escena y continuas. 

"spare_chunks": chunks que aparecen en la lista de escenas, pero no han sido parseados en las escenas del juego. No los he mirado a fondo, pero creo que son las muertes :).

## formato de las escenas y sequcencias

las sequencias estan definides en una struct de 0x2A bytes y parece que tiene este formato:

```c
struct chunk {
        frame *ptr_frame_start ; // frame where the sequence starts))
        frame *ptr_frame_end ; // frame where the sequence end))
        frame *ptr_frame_hitbox_start ; // frame  starts where the hitbox is valid
        frame *ptr_frame_hitbox_end ; // frame ends where the hitbox is valid
        hitbox *ptr_hitbox ; // ptr to the hitbox 
        frame *ptr_frame_unk ;// frame unknown
        chunk *ptr_node_respawn ;// chunk sequenche on respawn ( kinda save point)
        
        int_8 field_0 ;// unk
        int_8 field_1;// unk
        int_8 type_a ;// unk
        int_8 type_b ;// unk
        int_8 num_sequences // num the sequencies in the  list_sequences.
        int_8 type_d = ;// unk
        void *ptr_fn_callback ;// callback function 
        chunk **ptr_list_sequences ;// list of ptrs to chunks, usually 0-> on death 1->on hit first hitbox 2-> on hit second hitbox...
}

```