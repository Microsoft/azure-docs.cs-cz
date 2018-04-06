<!--author=alkohli last changed: 12/15/15-->

| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovacích údajů účtu úložiště |64 | |
| Maximální počet kontejnery svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet plány na šablony šířky pásma |168 |Plán pro každou hodinu, každý den v týdnu (24 * 7). |
| Maximální velikost vrstvený svazek na fyzických zařízení |64 TB pro 8100 a 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální velikost vrstvený svazek na virtuálním zařízením v Azure |30 TB pro 8010 <br></br> 64 TB pro 8020 |8010 a 8020 jsou virtuální zařízení v Azure, které používají úložiště úrovně Standard a Premium Storage v uvedeném pořadí. |
| Maximální velikost místně vázaný svazek na fyzických zařízení |9 TB pro 8100 <br></br> 24 TB pro 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iniciátorů iSCSI |512 | |
| Maximální počet záznamů řízení přístupu podle zařízení |64 | |
| Maximální počet svazků na zásady zálohování |24 | |
| Maximální počet záloh uchovávány v zásady zálohování |64 | |
| Maximální počet plány na zásady zálohování |10 | |
| Maximální počet snímků žádný typ, který uchovávání může být na svazku |256 |Jedná se o místní snímky a cloudových snímků. |
| Maximální počet snímků, které můžou být v libovolném zařízení |10 000 | |
| Maximální počet svazků, které lze zpracovat paralelní zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje víc než 16 svazky, budou se zpracují postupně zpracování sloty dostupná.</li><li>Nové zálohy klonovaný nebo obnovený vrstvený svazek nemůže proběhnout, dokud je operace dokončena. Pro místní svazek, jsou však zálohy povoleny, po svazek je online.</li></ul> |
| Obnovení a klonování obnovit dobu vrstvené svazky |< 2 minuty |<ul><li>Svazek je k dispozici během 2 minut operaci obnovení nebo klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než běžné jako většinu dat a metadat se stále nachází v cloudu. Jako toky dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas ke stažení metadat, závisí na velikosti přiděleného svazku. Metadata se automaticky přenesou do zařízení na pozadí ve výši 5 minut za TB dat přidělené svazku. Tato míra může mít vliv šířky pásma Internetu do cloudu.</li><li>Obnovení nebo operaci klonování je dokončena po všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude obnovení nebo je plně dokončení operace klonování. |
| Obnovení obnovit dobu místně vázaných svazků |< 2 minuty |<ul><li>Svazek je k dispozici během 2 minut operaci obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než běžné jako většinu dat a metadat se stále nachází v cloudu. Jako toky dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas ke stažení metadat, závisí na velikosti přiděleného svazku. Metadata se automaticky přenesou do zařízení na pozadí ve výši 5 minut za TB dat přidělené svazku. Tato míra může mít vliv šířky pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvené svazky, v případě místně vázaných svazků data na svazku je také stažen místně na zařízení. Operace obnovení byla dokončena, pokud všechna data svazek znovu do zařízení.</li><li>Operace obnovení může trvat dlouho a celkovou dobu pro dokončení obnovení bude záviset na velikosti zřízené místní svazek, pásma vašeho internetového připojení a existující data v zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, když probíhá operace obnovení. |
| Dynamické obnovení dostupnosti |Poslední převzetí služeb při selhání | |
| Propustnost pro čtení a zápis maximálního počtu klientů (Pokud obsluhovat z vrstvy SSD) * |920/720 MB/s se jeden 10GbE síťové rozhraní |Až 2 x s funkci MPIO a dvě síťová rozhraní. |
| Propustnost pro čtení a zápis maximálního počtu klientů (Pokud obsluhovat z vrstvy HDD) * |120/250 MB/s | |
| Propustnost pro čtení a zápis maximálního počtu klientů (Pokud obsluhovat z vrstvy cloudu) * |11/41 MB/s |Propustnost čtení závisí na klientech generování a údržbu dostatečná hloubku fronty vstupně-výstupní operace. |

&#42;Maximální propustnost podle typu vstupně-výstupních operací se měří s 100 procent čtení a zápisu 100 procent scénáře. Skutečná propustnost může být nižší a závisí na vstupně-výstupních operací kombinovat a síťové podmínky.

