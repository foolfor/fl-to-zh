# **RISC-V3: A RISC-V Compatible CPU With a Data Path Based on Redundant Number Systems**

**MARC REICHENBACH , JOHANNES KNÖDTEL, SEBASTIAN RACHUJ, AND DIETMAR FEY**

**ABSTRACT** Redundant number systems (RNS) are a well-known technique to speed up arithmetic circuits. However, in a complete CPU, arithmetic circuits using RNS were only included on subcircuit level e.g. inside the Arithmetic Logic Unit (ALU) for realization of the division. Still, extending this approach to create a CPU with a complete data path based on RNS can be beneficial for speeding up data processing, due to avoiding conversions in the ALU between RNS and binary number representations. Therefore, with this paper we present a new CPU architecture called RISC-V3 which is compatible to the RISC-V instruction set, but uses an RNS number representation internally to speed up instruction execution times and therefore increase the system performance. RISC-V is very suitable for RNS because it does not have a f;ags register which is expensive to calculate when using an RNS. To present reliable performance numbers, arithmetic circuits using RNS were realized in different semiconductor technologies. Moreover, an instruction set simulator was used to estimate system performance for a benchmark suite (Embench). Our results show, that we are up to 81% faster with the RISC-V3 architecture compared to a binary one, depending on the executed benchmark and CMOS technology.

**INDEX TERMS** Arithmetic and logic units, processor architectures, RISC, ternary arithmetic.

# I. INTRODUCTION

For building fast and energy efcient computer systems, one possibility is to consider alternative number representations other than traditional 2's complement. An approach proposed in the past is to use redundant number systems [1]. These number systems allow the usage of more than r values per digit for a radix r > 1. While this looks very inefcient on the rst glance in terms of hardware resources especially for storing data, arithmetic calculations can be performed asymptotically faster with regards to the word width when using these numbers. The reason for this improvement lies within the limited carry propagation of arithmetic circuits implementing such number systems.

Parhami [2] proposed GSD (General Signed-Digit) number systems as a framework for different redundant number representations and its arithmetic operations (e.g. additions) on it. As special cases for radix-2 based systems (r D 2) BSD (binary signed-digit), with the values f􀀀1; 0;C1g, and BSC(binary stored-carry) with the values f0;C1;C2g are covered by this framework. By utilizing these number representations, additions can be executed in constant time, independent of the word length. This fact can be exploited to speed up arithmetic in modern CPUs. Moreover, addition can be seen as the basis of most arithmetic operations. Optimizing this operation is potentially able to increase a CPUs performance dramatically. For example some oating-point units are utilizing RNS in their internal architecture to perform the SRT division algorithm [3], [4]. Finally, limiting carry propagation and increasing data locality decrease CMOS net activity for sufciently large word sizes and is therefore able to save energy.

> The associate editor coordinating the review of this manuscript and approving it for publication was Sun Junwei .

In this paper, especially BSD and BSC number representations are used, because these systems allow the implementation with traditional full adder structures. Using full adder cells promises a short critical path because highly optimized versions are often provided in most standard cell libraries. Fig. 1 shows an example for the addition of two numbers in BSC representation. As discussed, a number in this number representation may not only contain "1"s and "0"s as digits; additionally, we allow the "2" as digit value here. Consequently, instead of propagating a carry, the carry will be included in the appropriate digit, which will be possible by allowing a "2" as digit for the number representation. Due to the possibility to allow three different states per digit, we will call these number representations in this paper ternary number systems.

![图1](.\RISC-V3\图1.PNG)

FIGURE 1. Addition example in BSC. The reason that this example can be calculated in constant time is that the carries can only affect at most two digits in the result. Note that is possible to continue calculating with the resulting number without eliminating ``2''s from the result and still retaining the constant time delay, as shown in section II.



In the area of redundant number systems a lot of research has been done. Parhami [5] rst proposed a recode mechanism for BSD numbers, to allow a carry free-addition. For BSC,Weinberger [6] proposed the 4-2-Adder Module, which was re-analyzed several times in context of GSD [7], [8]. This circuit is usually known as a carry-save adder. Based on these fundamental ndings more recent work has been done: For example in [9] and [10] new FPGA implementations of arithmetic circuits using RNS have been developed. Moreover, various applications could be sped up using RNS. Examples inclue complex DSP calculations using CORDIC [11] or the fast computation of cryptographic functions [12].

![图2](.\RISC-V3\图2.PNG)

FIGURE 2. RNS CPU compared to traditional ones. The upper part shows a conventional CPU data path where only a few functional units (like SRT) use a RNS (marked in red). In the lower part a CPU is depicted that uses uses RNS as the default number representation (also marked in red) and only converts back for certain operations (like logical operations).



As shown, redundant number representations might be able to increase arithmetic subcircuit performance signicantly and are therefore used in rare cases, e.g. for the SRT division in the arithmetic logic unit (ALU) of a CPU. However, due to the non-unique representation of a number in RNS meaning that a value z can be represented by different bit vectors in RNS, a time-consuming (non-constant time) conversion becomes necessary for further arithmetic operations using this value in classical 2's complement. For example, in a CPU that uses classical 2's complement representation in general but also a RNS-based arithmetic subcircuits for division, a conversion is needed for each division instruction inside the ALU. As Parhami already stated, "Whenever long sequences of computations are to be performed on particular pieces of data, the one-time conversion and reconversion effort to/from the OSD [ordinary signed digit] representation is more than compensated for by the gain in computation speed." [2]. Hence, it is useful to increase the length of these sequences. Therefore, for a further performance increase, this implies not only using RNS based arithmetic subcircuits, but rather to completely rely on ternary encodings on the data path in the CPU. Consequently, conversions between/to registers and the ALU in a CPU will be avoided. Finally, all instructions in a CPU are working on words in RNS representation which avoids the conversation completely (except of Input/Output of the CPU). In Fig. 2 a comparison between a traditional CPU and a ternary CPU as presented in this paper is shown. While in the traditional CPU only a small subcircuit (SRT) inside the ALU works with RNS (marked in red), in the ternary CPU the complete data path is implemented based on RNS. There are still circuits relying on binary representations, as they are unavoidable, e.g. bit-wise operations such as bit-wise and, but signals between functional units are encoded entirely in RNS. Although, the above mentioned method promises a huge performance gain, different challenges ariseV

1) Due to more bits having to be stored for RNS numbers than actually required for conventional binary ones, register les inside the CPU increase in size as well as energy consumption.

2) While arithmetic circuits such as addition and multiplication will become faster and more efcient using RNS, other operations such as shifts, bit-wise logical operations, and sign detection are more complex than their counterparts using 2's complement.

3) By executing several consecutive arithmetic operations using RNS, results might need more digits to be stored. While this might look as an integer overow, the corresponding value is still in the valid range. This topic was covered in previous works by different authors and the problem was coined pseudo-overow by Timmermann and Hosticka [13].

To really benet from the ternary CPU, the above shown challenges (1) to (3) need to be addressed in detail and will be analyzed in this paper. Therefore, we want to investigate the additional overhead which is needed to solve these problems. Fortunately, research activities to address problem (1) have been done before. To overcome it, the authors of [14] propose to use novel emerging memories technologies such as ReRAM, which are able to store multiple states in one memory cell and therefore drastically improve the memory density.

However, problem (2) is a crucial limitation for processor designers and one of the main reasons, why this number representation is not used more often. While addition and other operations based on addition can be executed very fast in redundant number representations as seen before, other operations such as bit-wise logic operations, overow detection as well as sign detection cannot be executed in constant time, i.e. independent of the word length. In most cases a so-called renormalization becomes necessary which can be simply described as the conversion back into a conventional number respresentation. However, this needs the same amount of time as an addition in traditional 2's compliment representation. Nevertheless, even if a few operations cannot be sped up, a performance improvement of programs running on the system as a whole is still possible. We will prove this claim in this paper by providing a system simulator which evaluates the overall performance of the ternary CPU compared with a binary one.

In ternary arithmetic subcircuits with binary external interfaces, problem (3) does not occur, because the number is converted back to the traditional 2's complement representation after the calculation which is equivalent to a complete renormalization. Unfortunately, in a CPU with a ternary data path an arbitrary number of arithmetic operations can be done on a register without any renormalization process. Due to their redundant behavior, this will result in a large number of leading digits [15] which does not t in the digits provided by the registers although the corresponding binary value would t. In this paper, we will propose an adapted normalization process to solve this problem. It can be executed in constant time inside the adder circuit, independent from the word length.

As one can see, a deep analysis of ternary CPU architectures is needed. On the one hand side, RNS promises a huge performance gain due to optimized arithmetic, on the other hand side, new drawbacks arise which did not exist in traditional computers using 2's complement computers. Therefore, in this paper we will provide an extended analysis of RNS data paths and their impact on complete CPUs. This will include a methodology on how to address the above mentioned challenges. Finally, we create a new CPU architecture, based on RISC-V, with a ternary data path, utilizing BSC and BSD RNS as well as an evaluation methodology to evaluate chances and risks of using RNS in CPUs.

This paper contains 6 Sections, organized in an order, that follows a bottom-up approach. In this Section, we provided an introduction and gave a historical background. In Section II, we rst design and present our basic arithmetic circuits using RNS (including conversion functions to 2's complement) and show their performance. This enables a quantitative analysis of these RNS in terms of timing. Afterwards, in Section III, we present our RISC-V system architecture using a ternary data path and map the determined timings, found in the previous section, to this architecture. Using this approach, we are able to run simulations to estimate timing behavior of the whole processor. This will be focused on in Section IV. There, we present our complete simulation environment and how it is possible to run real (benchmark) programs on the new ternary architecture. Finally, in Section V, we do an extended evaluation and compare the measured results using our new processor architecture with traditional ones using 2's complement. Section VI will summarize and conclude this paper.

Summarizing, our contributions in this paper are:

- A study on ternary circuit implementations containing arithmetic circuits for different RNS and their mapping to different CMOS technologies. This resulted in a characterized VHDL IP library.
- A concept of a CPU architecture with ternary data path based on the RISC-V ISA.

- A simulation environment to execute benchmark programs on the ternary CPU architecture.

- An evaluation methodology, i.e. providing a set of experiments to estimate the overall system performance. This methodology is able to translate knowledge about arithmetic performance to system level performance metrics.



# II. CIRCUIT DESIGN FOR RNS ARITHMETIC

As described above, in this chapter we will design our arithmetic circuits in different redundant number systems. Due to the fact that conventional CMOS technology does not offer the means for processing ternary values, the functionality has to be realized using binary signals. Thus, we rst present different encoding schemes to realize numbers in RNS with conventional binary technologies. After that, we derive the arithmetic circuits for addition on these number representations and nally we present our synthesis results, which shows the benets regarding timing behavior over the binary representation.

## A. NUMBER REPRESENTATIONS AND ENCODINGS 

The BSD and BSC number representations are both based on the radix r D 2. Due to the redundant nature of these number representations, each digit di in a BSC or BSD number z, has to hold one out of three possible states, which needs to be encoded with binary signals (bits). For the encoding of three possible states, at least two bits are required. With an encoding table we will dene, how a set of two bits is interpreted in their respective number representation. This encoding heavily inuences the arithmetic circuits as well as the synthesis results. In BSD representation each digit di is dened as di 2 f􀀀1; 0;C1; g, resulting in a BSD number

![公式1](.\RISC-V3\公式1.PNG)

Now, each di needs to be encoded by two bits which leads us to three encodings: (i) the so called positive/negative (BSD-PN) encoding, (ii) sign/value (BSD-SV) encoding and (iii) encoding we called the BSD-SUM encoding, as described by [2], [16]. The name BSD-SUM is used, because its encoded value can be derived directly from the sum of both individual bits (i.e. d(1)Cd(2)), which means that d D (0; 1) and d D (1; 0) encoding the same encoded value (namely 0). For our experiments we will only use BSD-PN and BSD-SUM encoding, as it has the advantage, that the inversion of the number is simply done by inversion the bits of the digit. BSD-SV is not further investigated in this paper. 

![公式2](.\RISC-V3\公式2.PNG)



TABLE 1. Encoding table for BSC and BSD number representation.

![表1](.\RISC-V3\表1.PNG)



![图3](.\RISC-V3\图3.PNG)

FIGURE 3. Example for different encodings with different in RNS.



For BSC representation a number z is defined similarly with

![公式3](.\RISC-V3\公式3.PNG)

Further, each digit di is dened as di 2 f0;C1;C2g. As encoding we choose here the sum of the two representing bits, leading to di D d(1) i C d(2) i . We will call this encoding BSC-2C in the following. All of the discussed encodings are formally described in Table 1. In Fig. 3 an example is shown, how a number z can be described in different RNS with different encodings. Furthermore, we dene z(1) and z(2) as bit vectors in the following way:

![公式4](.\RISC-V3\公式4.PNG)



While for BSD numbers, it is easy to represent negative numbers due to the presence of negative digits, for numbers in BSC representation an additional explanation howto represent negative numbers is needed. According to the fact, that in BSC-2C encoding, a digit is encoded as the sum of the two corresponding bits, also the overall number zBSC can be interpreted as the sum of the two vectors z(1) and z(2) resulting in zBSC D z(1) C z(2). By interpreting the numbers z(1) and z(2) in two's complement, it becomes possible to represent egative numbers.

![图4](.\RISC-V3\图4.PNG)

FIGURE 4. Adder circuit for BSD-PN encoding.



### B. BASIC ARITHMETIC CIRCUITS (ADDERS) 

Based on the three different encodings (BSD-PN, BSD-SUM und BSC-2C) adders can now be defined on circuit level. In Figure 4, Figure 5 and Figure 6 the resulting adder circuits are shown for performing a calculation of z D a C b. As basic element a classical full adder (FA) is used with some inverters. In contrast to a traditional adder (like a ripple carry adder) carry chains are limited to only one connection between FAs of one result digit in this implementation, resulting in the aforementioned constant critical path independent of the word length. Nevertheless, two additional bits (carryin) c(1) and c(2) are also defined, which can be used for additional arithmetic operations (e.g. subtractions). Furthermore, it can be seen that the circuit for BSD-SUM and BSC-2C encoding looks very similar except of the most significant digit. 

While the presented arithmetic circuits allow performing an addition, the subtraction can also be implemented. Based on the knowledge of traditional adders, a subtraction is performed by adding the negated number, i.e. z = a-b = a+(-b). Defining the negation of a number z as neg(z) = -z, the calculation of neg(zBSD) can, as mentioned previously for BSD-PN and BSD-SUM, easily be performed by evaluating

![公式5](.\RISC-V3\公式5.PNG)



and z(2) respectively which can be performed by using simple XOR gates. The negation of zBSC will be slightly more complex. As dened zBSC D z(1) C z(2) results in

![公式6](.\RISC-V3\公式6.PNG)



by applying two's complement for the negation of z(1) and z(2). The constant values (1) can be used as input c in the represented adder circuit.



![图5](.\RISC-V3\图5.PNG)

FIGURE 5. Adder circuit for BSD-SUM encoding.



![图6](.\RISC-V3\图6.PNG)

FIGURE 6. Adder circuit for BSC-2C encoding.



An even more efficient variant exists for BSD-PN, by swapping the constituent bits of each digit:

![公式7](.\RISC-V3\公式7.PNG)



### C. DESIGN AND CHARACTERIZATION OF ADDERS

In order to get quantitative results we characterized the delay of the presented circuits in standard cell based designs by post-synthesis static timing analysis (STA). The methodology of this characterization is as follows: First the designs were synthesized and afterwards analyzed by static timing using an industry-standard synthesis tool. This is done by applying different constraints to force the tool to yield delay-optimal results. These timing constraints for finding the lowest critical path were found by binary search: this search is stopped after a number of steps where the maximum error of the search was smaller than 3 picoseconds, which is well within the error margin of the STA and synthesis. We found that due to the nature of the optimization algorithms in the synthesis tool, we do not get a constant delay for different word length. Nevertheless, the uncertainties are minor and therefore can be ignored.

![图7](.\RISC-V3\图7.PNG)

FIGURE 7. Speedup of RNS adders compared to a binary adder.



Our experiments are carried out utilizing different PDKs:

- Vendor A at 150nm (Low Vt (High Speed), High Vt (Low Power))
- Vendor B at 250nm, 130nm
- Skywater 130nm (Medium Speed, Low Speed, High Density)

Due to contractual obligations we cannot disclose the identity of Vendor A and Vendor B, but we are using regular standard cell libraries provided to us.

The results of this delay characterization can be found in Figure 7. It shows the speedup relative to the fastest binary adder circuit for different word widths for each technology (design kit). Due to the fact, that the adders for BSC-2C and BSD-SUM result in a similar circuit with the same critical path these two graphs are merged. It can be seen, that speedups up to 2.5x are possible for 128 bit word width in Skywater PDK.

In contrast to intuition, counting gates in the critical path is not representative for the delay of the associated timing arcs, as this is highly dependent on standard cell properties and capacities of interconnects between cells and input ports. These synthesis results suggest that e.g. the BSD-PN adder is faster than the BSD-SUM/BSC-2C adder although it has additional inverters.

### D. CONSECUTIVE ADDITIONS AND NORMALIZATION

While in the previous chapters a simple addition was realized, the question arises whether such circuits can now be placed easily in the ALU of a CPU. Therefore, in this chapter we want to elaborate on consecutive additions and their impact on the corresponding adder circuits. In Figure 8 an example is shown for two consecutive additions in BSC representation for n D 5 digits. Initially a and b are given by a D 1 and b D 􀀀1 and shown in their corresponding representation with a(1) and a(2), and b(1) and b(2) respectively. After calculating c D a C b with the aforementioned adder circuits, the corresponding vectors.

![图8](.\RISC-V3\图8.PNG)

FIGURE 8. Example of consecutive additions in a BSC-2C encoding with the initial values a = 1 and b = -1. In the first step c = a + b, in the second d = c + a = 1 + (-1) + 1 = 1 is calculated. The results contains two leading digits (underlined), which cannot be omitted.



c(1) and c(2) are also shown, which are yielding the correct result. In order to stress the effects to be obvious here, c(1) and c(2) contain two leading digits which are not necessary for the result to be correct. Finally, d D c C a is calculated. Also here, d(1) and d(2) has two leading digits. In contrast to the calculation of c it can be seen, that these two digits cannot be omitted without altering the resulting value of the number. 

This example leads to a problem which was already described before in [15] as ``carries may ripple out into positions which may not be needed to represent the nal value of the result and, thus, a certain amount of leading guard digits are needed to correctly determine the result''. This means, that although if n digits are enough to encode a value, the result of an addition is a number z with the same value, but which requires n C 2 digits due to the structure of the adder circuit and the redundant nature of the number system.

For building a CPU with ternary data path this fact will cause a problem which has to be analyzed very carefully:Will the effect of leading digits accumulate over consecutive additions? This question is analyzed by a second example, shown in Figure 9. The two leading digits (underlined) for c cannot be removed easily: to remove them, a total renormalization of the whole number is needed. Total renormalization means, to convert the result completely to its non-redundant binary form and therefore allow storing the information in a binary compatible form in one of the vectors. Unfortunately, this is of course expensive and should be avoided. The delay of the total renormalization circuits is dominated by the adder or similar component used for the conversion to binary. To put these costs into perspective: The ternary adder is up to 4 times faster than the renormalization circuit.

![图9](.\RISC-V3\图9.PNG)

FIGURE 9. Example of consectuively additions in BSC representation with handling of leading digits. Initially the constants are set to a D 􀀀116 and b D 􀀀8. Afterwards c D aCb D 􀀀124 is calculated by needing two leading digits (underlined). Finally, we set d D 􀀀3 and calculate e D c Cd D 􀀀127. It can be seen, that e requires again two additional leading digits (double underlined). Fortunately, the four additional leading digits (two underlined and two double underlined) can be condensed to only two leading digits, by performing a so-called constant-time partial normalization en D norms(e) and manipulating only the four leading bit.



Summarizing, it is clear, that it is not useful to perform a total renormalization after each addition. Therefore, it is a good idea to accept the additional generated leading digits for now, but investigate if they will increase after an additional addition step. Fortunately this is not the case as they will not accumulate over consecutive additions, after performing a so-called partial normalization after each addition step, taking only a constant amount of time. Referring again to Figure 9 the leading digits (underlined, as shown in the number e) can be condensed by a constant-time normalization (en D norms(e)) which only needs to consider a constant number of leading digits, independently of the word width. For a formal description, two functions are introduced: head(z(i)) provides the four leading digits of a given vector z(i) while tail(z(i)) provides the remaining ones. Furthermore, if CC is dened as the concatenation of two bit-vectors, then we can dene zn D norms(z) as follows and apply it to determine en as shown in the example.

![公式8](.\RISC-V3\公式8.PNG)



In [15] the leading-digits problem and its normalization is addressed in a mathematical way; also [13] shows a specic algorithm for SD numbers. Unfortunately, to our best knowledge, an RTL or lower level implementation is missing but needed for the investigation of these adders in a ternary CPU. Therefore in addition to the basic adder circuits, we provide normalization circuits for dealing with leading digits in this paper. Nevertheless, in the work of [15] and [13] multiple mathematical descriptions for constant-time normalization are shown, so the following three different circuits were implemented:

- 1) Adder-Normalization: as dened by norms(z); A generic method by us, which requires two leading digits. Can be used for BSD-PN, BSD-SUM and BSC-2C representation. This is loosely based on the proofs of [15].

- 2) XOR-Normalization: Method presented in [15] (Corollary 9, similar to [17], [18]) which requires only one leading digit. Can only be used for BSC-2C.

- 3) MUX-Normalization: Method presented in [13] which requires also one leading digit. Can only be used for BSD-PV and BSD-SUM.

![图10](.\RISC-V3\图10.PNG)

FIGURE 10. Adder-based normalization scheme.





![图11](.\RISC-V3\图11.PNG)

FIGURE 11. XOR-normalized adder for BSC-2C.





![图12](.\RISC-V3\图12.PNG)

FIGURE 12. Mux-based normalization scheme.



The described normalization circuits are shown in Figures 10, 11 and 12. Finally the MUX-Normalizer and the Adder-Normalizer need to be placed directly behind the basic adder circuit to reduce the length of nC3 (three leading digits) to n C 1 (one leading digits) after each addition. The XOR-Normalizer is placed after each layer of FAs inside of the adder circuit and is therefore integrated into it.



TABLE 2. Overview about all investigated circuits. Left different RNS and its encodings, at the top different normalization circuits. A checkmark means that this combination will be considered.

![表2](.\RISC-V3\表2.PNG)



Our ndings result in Table 2. Here, all investigated circuits (combination of an adder and a normalization method) are shown. Summarizing, a constant time normalization should be performed after each addition step to make further calculations possible. However, full renormalization takes very long but is only needed in some special cases, especially when the binary representation of a number is exactly needed. That will be the case for example, if the result of an addition will be used by bit manipulating operators. 

Finally, as presented before, the resulting circuits (adder with constant time normalization) have been evaluated in the same way, as the basic adder circuits. In Fig. 13 the results are shown. It is clear, that the speedup decreases by using these normalization circuits. However, such circuits are needed and denitively have to be considered when constructing a ternary CPU. Nevertheless, even a speedup of around two is still achievable for 128 bits registers. It can be seen, that BSD-SUM and BSD-PN with MUX-Normalization will give the best results, especially for Vendor B 130nm process. Therefore, we will focus on these numbers for building the ternary CPU.

When analyzing the other technologies, the results are worse compared to Vendor B 130nm technology. The main reason for these discrepancies are differences in standard cell libraries optimized for speed or other metrics: Here especially the delays incurred from gates and their output nets are different. This is hard to analyse since delays are affected by capacitances, but we saw this effect rather consistently. Thereby, the greater speedup of Vendor B 130nm can beexplained.

![图13](.\RISC-V3\图13.PNG)

FIGURE 13. Speedup over binary adder for normalized redundant adders.









# III. RISC-V3: A TERNARY RISC-V ARCHITECTURE





# IV. SIMULATION ENVIRONMENT



# V. EVALUATION



# VI. DISCUSSION AND CONCLUSION



# REFERENCES

[1] A. Avizienis, "Signed-digit numbe representations for fast parallel arithmetic,"

*IEEE Trans. Electron. Comput.*, vol. EC-10, no. 3, pp. 389-400, Sep. 1961.

[2] B. Parhami, "Generalized signed-digit number systems: A unifying framework for redundant number representations," *IEEE Trans. Comput.*, vol. 39, no. 1, pp. 89-98, Jan. 1990.

[3] T. E. Williams and J. Horowitz, "SRT division diagrams and their usage in designing custom integrated circuits for division," Comput. Syst. Lab., Dept. Elect. Eng., Stanford University, Stanford, CA, USA, Tech. Rep. CSL-TR-87-328, 1998.

[4] D. L. Harris, S. F. Oberman, and M. A. Horowitz, "SRT division architectures and implementations," in *Proc. 13th IEEE Symp. Comput. Arithmetic*, Jul. 1997, pp. 18-25.

[5] B. Parhami, "Carry-free addition of recoded binary signed-digit numbers," *IEEE Trans. Comput.*, vol. 37, no. 11, pp. 1470-1476, Nov. 1988.

[6] A. Weinberger, "4-2 carry-save adder module," IBM Tech. Discl. Bull., Tech. Rep., 1981.

[7] P. Kornerup, "Reviewing 4-to-2 adders for multi-operand addition," *J. VLSI Signal Process. Syst. Signal, Image, Video Technol.*, vol. 40, no. 1, pp. 143-152, May 2005.

[8] M. D. Ercegovac and T. Lang, Digital Arithmetic (The Morgan Kaufmann Series in Computer Architecture and Design). San Francisco, CA, USA: Morgan Kaufmann, 2004, pp. 50-135.

[9] Y. Tanaka, Y. Suzuki, and S. Wei, "Novel binary signed-digit addition algorithm for FPGA implementation," *J. Circuits, Syst. Comput.*, vol. 29, no. 09, Jul. 2020, Art. no. 2050136.

[10] R. Thakur, S. Jain, and M. Sood, "FPGA implementation of unsigned multiplier circuit based on quaternary signed digit number system," in *Proc. 4th Int. Conf. Signal Process., Comput. Control (ISPCC)*, Sep. 2017, pp. 637-641.

[11] H. Mahdavi and S. Timarchi, "Improving architectures of binary signeddigit CORDIC with Generic/Specific initial angles," *IEEE Trans. Circuits Syst. I, Reg. Papers*, vol. 67, no. 7, pp. 2297-2304, Jul. 2020.

[12] S. Wei, "New residue signed-digit addition algorithm," *Adv. Intell. Syst., Comput.*, vol. 1070, pp. 390-396, Jun. 2020.

[13] D. Timmermann and B. J. Hosticka, "Overow effects in redundant binary number systems," *Electron. Lett.*, vol. 29, no. 5, pp. 440-441, Mar. 1993.

[14] D. Fey, M. Reichenbach, C. Söll, M. Biglari, J. Röber, and R. Weigel, "Using memristor technology for multi-value registers in signed-digit arithmetic circuits," in *Proc. 2nd Int. Symp. Memory Syst.*, New York, NY, USA, Oct. 2016, pp. 442-454.

[15] P. Kornerup and J.-M. Muller, "Leading guard digits in finite precision redundant representations," *IEEE Trans. Comput.*, vol. 55, no. 5, pp. 541-548, May 2006.

[16] G. Jaberipur, B. Parhami, and M. Ghodsi, "An efcient universal addition scheme for all hybrid-redundant representations with weighted bit-set encoding," *J. VLSI signal Process. Syst. Signal, Image Video Technol.*, vol. 42, no. 2, pp. 149-158, Feb. 2006.

[17] T. Noll and E. De Man, "Anordnung zur bitparallen addition von binärzahlen mit carry-save überlaufkorrektur," U.S. Patent 0 249 132 B1, Aug. 14, 1993.

[18] T. G. Noll, "Carry-save architectures for high-speed digital signal processing," *J. VLSI Signal Process. Syst. Signal, Image Video Technol.*, vol. 3, nos. 1-2, pp. 121-140, Jun. 1991.

[19] A. Waterman and K. Asanovi¢, *The RISC-V Instruct Set Manual*, vol. 1. San Francisco, CA, USA: RISC-V Foundation, 2019.

[20] K. Asanovi¢ and D. A. Patterson, "Instruction sets should be free: The case for RISC-V," EECS Dept., Univ. California, Berkeley, CA, USA, Tech. Rep. UCB/EECS-2014-146, 2014.

[21] A. Hagelauer, R. Weigel, M. Reichenbach, and D. Fey, "Integrierte memristor-basierte Rechner-Architekturen (IMBRA)," Res. DFG Proposal 53.01-05/16, 2017.

[22] B. Parhami, "Tight upper bounds on the minimum precision required of the divisor and the partial remainder in high-radix division," *IEEE Trans. Comput.*, vol. 52, no. 11, pp. 1509-1514, Nov. 2003.

[23] B. Parhami, "Precision requirements for quotient digit selection in highradix division," in *Proc. 34th Asilomar Conf. Signals, Syst. Comput.*, 2001, pp. 1670-1673.

[24] C. Yu Hung and B. Parhami, "Generalized signed-digit multiplication and its systolic realizations," in *Proc. 36th Midwest Symp. Circuits Syst.*, Aug. 1993, pp. 1505-1508.

[25] T. Nishiyama and S. Kuninobu, "Divider and arithmetic processing units using signed digit operands," U.S. Patent 4 935 892, Jun. 19, 1990.

[26] S. Rachuj, C. Herglotz, M. Reichenbach, A. Kaup, and D. Fey, "A hybrid approach for runtime analysis using a cycle and instruction accurate model," in *Proc. Int. Conf. Archit. Comput. Syst.* Cham, Switzerland: Springer, 2018, pp. 85-96.

[27] S. Rachuj, D. Fey, and M. Reichenbach, "Impact of performance estimation of fast processor simulators," in *Proc. Simulation Tools, Techn. (SIMUtools).* Berlin, Germany: Springer, 2020.

[28] F. Bellard, "QEMU, a fast and portable dynamic translator," in *Proc. USENIX Annu. Tech. Conf.*, vol. 41, 2005, p. 46.

[29] S.-H. Kang, D. Yoo, and S. Ha, "TQSIM: A fast cycle-approximate processor simulator based on QEMU," *J. Syst. Archit.*, vols. 66-67, pp. 33-47, May 2016.

[30] Y. Luo, Y. Li, X. Yuan, and R. Yin, "QSim: Framework for cycleaccurate simulation on out-of-order processors based on QEMU," in *Proc. 2nd Int. Conf. Instrum., Meas., Comput., Commun. Control*, Dec. 2012, pp. 1010-1015.

[31] D. Thach, Y. Tamiya, S. Kuwamura, and A. Ike, "Fast cycle estimation methodology for instruction-level emulator," in *Proc. Design, Autom. Test Eur. Conf. Exhib. (DATE)*, Mar. 2012, pp. 248-251.

[32] L. Eeckhout, *Computer Architecture Performance Evaluation Methods* (Synthesis Lectures on Computing Architecture). San Rafael, CA, USA: Morgan & Claypool, 2010.

[33] R. M. Tomasulo, "An efcient algorithm for exploiting multiple arithmetic units," *IBM J. Res., Develop.*, vol. 11, no. 1, pp. 25-33, 1967.

[34] J. Hennessy and D. Patterson, *Computing Architecture: A Quantitative Approach*, 4th ed. Amsterdam, The Netherlands: Elsevier, 2007.

[35] F. Zaruba and L. Benini, "The cost of application-class processing: Energy and performance analysis of a linux-ready 1.7-GHz 64-bit RISC-V core in 22-nm FDSOI technology," *IEEE Trans. Very Large Scale Integr. (VLSI) Syst.*, vol. 27, no. 11, pp. 2629-2640, Nov. 2019.

[36] Embench. *Open Benchmarks for Embedded Platforms*. Accessed: Sep. 17, 2020. [Online]. Available: https://github.com/embench/embenchiot

# Author

**MARC REICHENBACH** received the Diploma degree in computer science from Friedrich-Schiller University Jena, in 2010, and the Ph.D. degree from the Friedrich-Alexander-Universität Erlangen-Nürnberg (FAU), in 2017. He currently works as a Postdoctoral Researcher with the Chair of Computer Architecture, FAU. His research interests include novel computer architectures, memristive computing, and smart sensor architectures for varying application fields.

**JOHANNES KNÖDTEL** received the M.Sc. degree in computer science from the Friedrich-Alexander-Universität Erlangen-Nürnberg (FAU), in 2016. He started as a Scientific Staff with the Chair for Computer Architecture, in 2017. His research interests include influence of arithmetic circuits on different levels of abstraction in the domain of processor design as well as the usage of memristive technologies in such designs.

**SEBASTIAN RACHUJ** received the B.Sc. degree in computer science from the Friedrich-Alexander-Universität Erlangen-Nürnberg (FAU), in 2015, and the M.Sc. degree in computer science from the Chair of Computer Architecture, Erlangen, in 2016. Since 2016, he has been working as a Scientific Staff with the Chair of Computer Architecture. His research interests include different approaches of processor simulation offering different speeds and accuracies and on the simulation of heterogeneous systems.

**DIETMAR FEY** received the Diploma degree in computer science and the Ph.D. degree with his work on using optics in computer architectures from the Friedrich-Alexander-Universität Erlangen-Nürnberg (FAU), in 1992, and the Habilitation degree from Friedrich-Schiller-University Jena. From 1994 to 1999, he researched at Friedrich-Schiller-University Jena. From 1999 to 2001, he worked as a Lecturer with Siegen University before he became a Professor of computer engineering with Jena University. Since 2009, he has been leading the Chair for Computer Architecture, FAU. His research interests include parallel computer architectures, programming environments, embedded systems, and memristive computing.