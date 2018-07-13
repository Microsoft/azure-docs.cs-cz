Následující tabulka popisuje každý z hlavní kvót, omezení, výchozí hodnoty a omezení ve službě Azure Scheduler.

| Prostředek | Popis omezení |
| --- | --- |
| **Velikost úlohy** |Maximální velikost je 16 kB. Pokud PUT nebo opravu výsledků v rámci úlohy, které jsou větší než tato omezení, se vrátí stavový kód 400 Chybný požadavek. |
| **Velikost adresy URL požadavku** |Maximální velikost požadavku na adresu URL je 2 048 znaků. |
| **Velikost agregační záhlaví** |Velikost hlavičky maximální agregace je 4096 znaků. |
| **Počet hlaviček** |Záhlaví maximální počet je 50 záhlaví. |
| **Velikost textu** |Tělo maximální velikost je 8192 znaků. |
| **Rozsah opakování** |Maximální počet opakování období je 18 měsíců. |
| **Čas spuštění** |Maximální počet "čas spuštění" je 18 měsíců. |
| **Historie úlohy** |Text maximální odpovědi, které jsou uloženy v historii úloh je 2 048 bajtů. |
| **Frekvence** |Výchozí kvóta maximální frekvence je 1 hodinu v kolekci úloh free a 1 minuta v kolekci standardní úloh. Maximální četnost je možné konfigurovat na kolekci úloh nižší než maximální délka. Všechny úlohy v kolekci úloh jsou omezena hodnotou nastavenou na kolekci úloh. Při pokusu o vytvoření úlohy s vyšší frekvencí, než je maximální četnost u kolekce úloh se požadavek nezdaří s stavový kód 409 konflikt. |
| **Úlohy** |Výchozí kvóta maximální počet úloh je 50 úlohy v kolekci standardní úloh a 5 úloh v kolekci úloh free. Maximální počet úloh, které je možné konfigurovat na kolekci úloh. Všechny úlohy v kolekci úloh jsou omezena hodnotou nastavenou na kolekci úloh. Pokud se pokusíte vytvořit víc úloh než kvóta maximální úlohy, požadavek selže s kódem stavu 409 konflikt. |
| **Kolekce úloh** |Maximální počet kolekci úloh na jedno předplatné je 200 000. |
| **Uchování historie úlohy** |Historie úlohy se uchovávají po dobu až 2 měsíců nebo až po poslední 1 000 spuštění. |
| **Dokončené a chybnou úloha uchovávání** |Dokončené a chybnou úlohy se uchovávají po dobu 60 dnů. |
| **časový limit** |Je statická časový limit (není konfigurovatelné) žádosti 60 sekund pro akce HTTP. Delší probíhajících operací postupujte podle asynchronní protokolů HTTP; například vrátit 202 okamžitě, ale pokračovat v práci na pozadí. |

