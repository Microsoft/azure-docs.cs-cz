| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na předplatné | 200<sup>1</sup> |
| Maximální kapacitě účtu úložiště | 500 TiB<sup>2</sup> |
| Maximální počet kontejnery objektů blob, objekty BLOB, sdílené soubory, tabulky, fronty, entity nebo zpráv na účet úložiště | Bez omezení |
| Rychlost maximální požadavků na účet úložiště | 20 000 požadavků za sekundu<sup>2</sup> |
| Příjem příchozích dat Max<sup>3</sup> na účet úložiště (nám oblastech) | 10 GB/s Pokud GRS nebo ZRS<sup>4</sup> povoleno, 20 GB/s pro LRS<sup>2</sup> |
| Maximální počet odchozí<sup>3</sup> na účet úložiště (nám oblastech) | 20 GB/s Pokud RA-GRS nebo GRS nebo ZRS<sup>4</sup> povoleno, 30 GB/s pro LRS<sup>2</sup> |
| Příjem příchozích dat Max<sup>3</sup> na účet úložiště (oblastech mimo USA) | 5 GB/s Pokud GRS nebo ZRS<sup>4</sup> povoleno, 10 GB/s pro LRS<sup>2</sup> |
| Maximální počet odchozí<sup>3</sup> na účet úložiště (oblastech mimo USA) | 10 GB/s Pokud RA-GRS nebo GRS nebo ZRS<sup>4</sup> povoleno, 15 GB/s pro LRS<sup>2</sup> |

<sup>1</sup>zahrnuje účty úložiště Standard a Premium. Pokud potřebujete více než 200 účtů úložiště, vytvořte žádost prostřednictvím [podpory Azure](https://azure.microsoft.com/support/faq/). Tým Azure Storage se na váš obchodní případ podívá a může schválit až 250 účtů úložiště. 

<sup>2</sup> Pokud potřebujete rozšířené limity pro váš účet úložiště, kontaktujte prosím [podporu Azure](https://azure.microsoft.com/support/faq/). Tým služby Azure Storage bude zkontrolujte požadavek a může schválit vyšší limity případ od případu. Obě pro obecné účely a účty úložiště Blob podporují zvýšené kapacity, vstupní/výstupní a rychlost požadavků požadavkem. Nové maximální hodnoty pro účty úložiště Blob, najdete v části [uvedení účty úložiště větší, vyšší škálování](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> omezená jenom ve vstupní/výstupní omezení účtu. *Příjem příchozích dat* odkazuje na všech dat (počet požadavků) odesílány do účtu úložiště. *Odchozí přenos dat* odkazuje na všechna data (požadavky) přijímané z úložiště účtu.  

<sup>4</sup>možnosti redundance azure Storage patří:
* **RA-GRS**: geograficky redundantní úložiště s přístupem pro čtení. Pokud je povoleno RA-GRS, odchozí cíle pro sekundární umístění jsou stejné jako pro primární umístění.
* **GRS**: geograficky redundantní úložiště. 
* **ZRS**: Zónově redundantní úložiště. K dispozici pouze pro objekty BLOB bloku. 
* **LRS**: místně redundantní úložiště. 
