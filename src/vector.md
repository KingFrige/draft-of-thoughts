# vector    

## architecture   

### performance requried
 
- pipeline
- decouple: LSU - EXU
- out-of-order: EXU

### arithmetic

- function  
- area  
- power  
- Manufacturability 
- fuse Multi-Mode
- lane & instance

### RegisterFile

- Manufacturability 
- multi-port: read & write
- harzds find
- size

### datapath

- width
- LOAD:  dram data -> L2 -> LSU -> RegisterFile
- STORE: RegisterFile -> LSU -> L2 -> dram

### pipeline

- 独立VectorUnit, 共用LSU
  * 简化rob  
  * 独立EXU   
  * tlb/ptw 与标量单元统一   

- 独立VectorUnit & LSU   
  * 独立EXU   
  * 独立LSU   
  * tlb/ptw 与标量单元分离   

- 融合的vector与scalar单元，拆解vector到superscalar的pipeline    
  * tlb/ptw 与标量单元统一   

- register bank   


### vconfig

  加快 `vset{i}vl{i}` 设置速度，可以将vconfig的参数与指令在decode阶段合并到micro-op中，直到commit阶段。

### chaining


