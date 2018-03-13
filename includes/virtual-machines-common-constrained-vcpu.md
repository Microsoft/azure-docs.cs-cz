

Některé databáze úlohy, jako je SQL Server nebo Oracle vyžadují velkého množství paměti, úložiště a vstupně-výstupní šířky pásma, ale není počet vysoké jader. Řadu úloh databáze nejsou náročná na prostředky procesoru. Azure nabízí určité velikosti virtuálních počítačů, kde můžete omezit počet virtuálních procesorů virtuálních počítačů k snížit náklady na licencování softwaru při zachování stejné paměť, úložiště a vstupně-výstupní šířky pásma.

Může být omezen počet virtuálních procesorů na polovina nebo jeden čtvrtletí původní velikost virtuálního počítače. Tyto nové velikosti virtuálních počítačů mají příponu, která určuje počet active Vcpu snadnější identifikaci.

Například aktuální velikost virtuálního počítače Standard_GS5 se dodává s 32 Vcpu 448 GB paměti RAM, 64 disků (až 256 TB) a 80 000 IOPs, nebo 2 GB/s šířky pásma vstupně-výstupní operace. Nový virtuální počítač velikosti Standard_GS5 16 a Standard_GS5-8 dodává s 16 a 8 active Vcpu, při zachování zbytek specifikace Standard_GS5 paměti, úložiště a vstupně-výstupní šířky pásma.

Licencování účtovat pro SQL Server nebo Oracle jsou omezené na počet virtuálních procesorů na nové, a další produkty by měly být účtovány poplatky podle počtu nových virtuálních procesorů. Výsledkem zvýšení 75 % na 50 % v poměru specifikace virtuálních počítačů do active Vcpu (fakturovatelný). Tyto nové velikosti virtuálních počítačů, které jsou dostupné jenom v Azure, povolení úlohy tak, aby nabízel vyšší využití procesoru za zlomek licencování (za jádra) náklady. V tuto chvíli na výpočetní náklady, což zahrnuje licencování operačního systému, zůstane stejný jeden jako původní velikost. Další informace najdete v tématu [velikostí virtuálních počítačů Azure pro další úlohy nákladově efektivní databáze](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Název                | Virtuální procesory | Specifikace           |
|---------------------|------|-----------------|
| Standard_M64-32ms   | 32   | Stejné jako M64ms   |
| Standard_M64 16ms   | 16   | Stejné jako M64ms   |
| Standard_M128-64ms  | 64   | Stejné jako M128ms  |
| Standard_M128-32ms  | 32   | Stejné jako M128ms  |
| Standard_E4 2s_v3   | 2    | Stejné jako E4s_v3  |
| Standard_E8-4s_v3   | 4    | Stejné jako E8s_v3  |
| Standard_E8-2s_v3   | 2    | Stejné jako E8s_v3  |
| Standard_E16-8s_v3  | 8    | Stejné jako E16s_v3 |
| Standard_E16-4s_v3  | 4    | Stejné jako E16s_v3 |
| Standard_E32 16_v3  | 16   | Stejné jako E32s_v3 |
| Standard_E32-8s_v3  | 8    | Stejné jako E32s_v3 |
| Standard_E64 32s_v3 | 32   | Stejné jako E64s_v3 |
| Standard_E64 16s_v3 | 16   | Stejné jako E64s_v3 |
| Standard_GS4-8      | 8    | Stejné jako GS4     |
| Standard_GS4-4      | 4    | Stejné jako GS4     |
| Standard_GS5-16     | 16   | Stejné jako GS5     |
| Standard_GS5-8      | 8    | Stejné jako GS5     |
| Standard_DS11-1_v2  | 1    | Stejné jako DS11_v2 |
| Standard_DS12-2_v2  | 2    | Stejné jako DS12_v2 |
| Standard_DS12-1_v2  | 1    | Stejné jako DS12_v2 |
| Standard_DS13-4_v2  | 4    | Stejné jako DS13_v2 |
| Standard_DS13-2_v2  | 2    | Stejné jako DS13_v2 |
| Standard_DS14-8_v2  | 8    | Stejné jako DS14_v2 |
| Standard_DS14-4_v2  | 4    | Stejné jako DS14_v2 |
