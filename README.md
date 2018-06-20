# Jean Grey
*Jean can manipulate other people's minds easily, achieving a variety of effects. The range at which she can perform these feats is greatly increased while she is accessing the Phoenix Force.*

JeanGrey is a tool to perform differential fault analysis attacks (DFA).

Currently it contains the following ciphers and fault models:

  * phoenixAES:  
    AES 128 encryption or decryption  
    At least 4*2 faults in round 9 (between the last two MixCols)  
    Ref: https://eprint.iacr.org/2003/010  
    Current implementation discards automatically unexploitable outputs but may fail if more than one fault occur on the same column so be careful to record only outputs from single faulted implementations.
  * phoenixAES.convert_r8faults:  
    AES 128 encryption or decryption  
    2 single faults in round 8
    It simply converts the ciphertexts as if they were faulted in round 9 so the previous attack can be applied

## Dependencies

This software only requires Python 3

## Usage

It takes a file of recorded outputs, optionally preceded by inputs (which will be ignored).
First record must be with the correct output, to be used as reference.  
E.g. for AES:

```python
#!/usr/bin/env python3
import phoenixAES

with open('tracefile', 'wb') as t:
    t.write("""
74657374746573747465737474657374 BF9B06F11DF478145B8300FE440B0D06
74657374746573747465737474657374 BF9BDDF11D527814568300FE440B0DFA
74657374746573747465737474657374 BF9BF9F11DAC78145F8300FE440B0D67
74657374746573747465737474657374 BF9BF0F11DBB78140C8300FE440B0DEE
74657374746573747465737474657374 BF9BF9F11DAC78145F8300FE440B0D67
74657374746573747465737474657374 BF9B69F11DBD7814E68300FE440B0DAE
74657374746573747465737474657374 BF9BF0F11DBB78140C8300FE440B0DEE
74657374746573747465737474657374 BF9B90F11D4178149D8300FE440B0DE2
74657374746573747465737474657374 BF9BCFF11D0478140E8300FE440B0D28
74657374746573747465737474657374 FD9B06F11DF478E15B831AFE44C40D06
74657374746573747465737474657374 BA9B06F11DF4787B5B83E8FE44020D06
74657374746573747465737474657374 579B06F11DF478565B8364FE446F0D06
74657374746573747465737474657374 579B06F11DF478565B8364FE446F0D06
74657374746573747465737474657374 BF9B065C1DF4B6145B1800FE9E0B0D06
74657374746573747465737474657374 BF9B065C1DF4B6145B1800FE9E0B0D06
74657374746573747465737474657374 BF9B06251DF454145BC200FE060B0D06
74657374746573747465737474657374 BF9B06941DF4C3145BFB00FED20B0D06
74657374746573747465737474657374 BF9B12F11D977814DD8300FE440B0D21
74657374746573747465737474657374 BF9B90F11D4178149D8300FE440B0DE2
74657374746573747465737474657374 BF9BCFF11D0478140E8300FE440B0D28
74657374746573747465737474657374 BF9BDDF11D527814568300FE440B0DFA
74657374746573747465737474657374 BFFB06F1E2F478145B8300AB440B7906
74657374746573747465737474657374 BF5D06F142F478145B830049440B7306
""".encode('utf8'))

phoenixAES.crack('tracefile')
```

After ~50 ms:

```
Last round key #N found:
D014F9A8C9EE2589E13F0CC8B6630CA6
```

For two single faults in round 8:

```python
#!/usr/bin/env python3
import phoenixAES

with open("r8faults", "w") as f:
    f.write("bf9b06f11df478145b8300fe440b0d06\n")
    f.write("fdfbf95ce2acb6e15f181aab9ec47967\n")
    f.write("ba5df02542bb547b0cc2e849060273ee\n")
phoenixAES.convert_r8faults("r8faults", "r9faults")
phoenixAES.crack("r9faults")
```

After ~50 ms:

```
Last round key #N found:
D014F9A8C9EE2589E13F0CC8B6630CA6
```
