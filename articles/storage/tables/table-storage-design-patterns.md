---
title: Vzory návrhu úložiště Azure table | Microsoft Docs
description: Využít vzory pro řešení služby tabulky Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 0d098b7befe5426db4aff503e9633623b1249dbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660768"
---
# <a name="table-design-patterns"></a>Vzory návrhu tabulky
Tento článek popisuje některé vzory vhodné používat s řešeními služby Table. Uvidíte taky může prakticky adresování některé z problémů a kompromis popsané v další tabulce úložiště návrhu články. Následující diagram shrnuje vztahy mezi různé vzorce:  

![k vyhledání související data](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Vzor mapy výše označuje některé vztahy mezi vzory (modré) a proti vzory (oranžová), které jsou popsané v této příručce. Existuje mnoho vzorů, které jsou vhodné s. Například jeden z klíčových scénářů pro služby Table je použití [Materializována vzor zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [příkaz dotazu odpovědnost oddělení (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) vzor.  

## <a name="intra-partition-secondary-index-pattern"></a>Vzor Intra-partition sekundární index
Uložit více kopií každou entitu s využitím různých **RowKey** hodnoty (ve stejném oddílu) k povolení rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých **RowKey** hodnoty. Aktualizace mezi kopie je možné mít konzistentní pomocí EGT společnosti.  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table automaticky indexuje entity pomocí **PartitionKey** a **RowKey** hodnoty. To umožňuje klientskou aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky vidíte níže, klientská aplikace pomocí dotazu bod k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle ID zaměstnance v rámci každé oddělení.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Pokud chcete být schopni najít entitu zaměstnanců na základě hodnoty, jiné vlastnosti, jako je například e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu k vyhledání shody. Je to proto, že služby table neposkytuje sekundární indexy. Kromě toho není žádná možnost odeslat žádost o seznam zaměstnanců seřazený v jiném pořadí než **RowKey** pořadí.  

### <a name="solution"></a>Řešení
Nedostatečná sekundární indexy obejít, můžete uložit více kopií každé entity s každou kopii pomocí jiné **RowKey** hodnotu. Pokud ukládáte entitu s struktury vidíte níže, můžete efektivně načíst zaměstnanec entit na základě ID e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **RowKey**, "empid_" a "email_" umožňují dotazu pro jeden zaměstnanců nebo rozsah zaměstnanci pomocí řadu e-mailové adresy nebo ID zaměstnance.  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Následující dvě kritéria filtru, která (jeden vyhledávání podle ID zaměstnance a jeden vyhledávání pomocí e-mailové adresy) i zadejte bod dotazy:  

* $filter = (PartitionKey eq 'Prodej') a (RowKey eq 'empid_000223')  
* $filter = (PartitionKey eq 'Prodej') a (RowKey eq 'email_jonesj@contoso.com')  

Pokud vyhledat rozsahu entit zaměstnanců, můžete zadat rozsah ID řazení zaměstnanců nebo rozsahu seřazeny v e-mailovou adresu pořadí pomocí dotazu pro entity v příslušnou předponu **RowKey**.  

* Všichni zaměstnanci z oddělení prodeje s zaměstnanec k vyhledání ID v rozsahu 000100 k 000199 použití: $filter = (PartitionKey eq 'Prodej') a (RowKey ge 'empid_000100') a (RowKey le 'empid_000199')  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, začínající písmenem "a" použití: $filter = (PartitionKey eq 'Prodej') a (RowKey ge 'email_a') a (RowKey lt 'email_b')  
  
  Všimněte si, že se používá ve výše uvedených příkladech syntaxe filtru je ze služby Table rozhraní REST API, další informace najdete v tématu [dotazu entity](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Table storage je relativně levný abyste nároky na náklady na ukládání duplicitních dat nesmí být závažný problém. Však měli vždy vyhodnoceny náklady návrhu na základě požadavků vaší předpokládaného úložiště a přidat pouze duplicitní entity, které podporují dotazy, které budou spuštěny klientské aplikace.  
* Proto sekundární index entity, které ukládají do stejného oddílu jako původní entity, musíte ověřit nepřekračují cíle škálovatelnosti pro jednotlivé oddíl.  
* Vzájemnou konzistenci duplicitní položky můžete ponechat pomocí EGTs dvě kopie entity, která atomicky aktualizovat. Z toho vyplývá, že měli uložit všechny kopie entity v stejného oddílu. Další informace najdete v části [pomocí transakce skupiny Entity](table-storage-design.md#entity-group-transactions).  
* Hodnota použitá **RowKey** musí být jedinečný pro každé entity. Zvažte použití hodnoty složeného klíče.  
* Odsazení číselných hodnot v **RowKey** (například zaměstnanci ID 000223), umožňuje opravte řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například pokud dotazy tento vyhledávací entity pomocí e-mailu adres v **RowKey** nikdy nepotřebují stáří zaměstnance, tyto entit může mít následující strukturu:

   ![Struktura zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Je obvykle lepší uložit duplicitní data a ujistěte se, že můžete načíst všechna data, je nutné se jeden dotaz, než chcete použijte jeden dotaz a vyhledejte entitu a druhý k vyhledání požadovaná data.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, když klientské aplikace potřebuje k načtení entity pomocí různých odlišné klíče, když váš klient potřebuje k načtení entity v jiné pořadí řazení, a tam, kde můžete identifikovat každé entity pomocí různých jedinečné hodnoty. Nicméně byste měli jistotu, že nedošlo k překročení omezení škálovatelnosti oddíl při provádění entity vyhledávání pomocí různými **RowKey** hodnoty.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor sekundární index mezi oddílů](#inter-partition-secondary-index-pattern)
* [Složené klíče vzor](#compound-key-pattern)
* [Transakce skupiny entity](#entity-group-transactions)
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Vzor sekundární index mezi oddílů
Uložit více kopií každou entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které chcete povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých **RowKey** hodnoty.  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table automaticky indexuje entity pomocí **PartitionKey** a **RowKey** hodnoty. To umožňuje klientskou aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky vidíte níže, klientská aplikace pomocí dotazu bod k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle ID zaměstnance v rámci každé oddělení.  

![ID zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Pokud chcete být schopni najít entitu zaměstnanců na základě hodnoty, jiné vlastnosti, jako je například e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu k vyhledání shody. Je to proto, že služby table neposkytuje sekundární indexy. Kromě toho není žádná možnost odeslat žádost o seznam zaměstnanců seřazený v jiném pořadí než **RowKey** pořadí.  

Jsou očekávání velmi velký objem transakcí proti tyto entity a chcete minimalizovat riziko omezení vašeho klienta služby Table.  

### <a name="solution"></a>Řešení
Nedostatečná sekundární indexy obejít, můžete uložit více kopií každé entity s každou kopírování pomocí různých **PartitionKey** a **RowKey** hodnoty. Pokud ukládáte entitu s struktury vidíte níže, můžete efektivně načíst zaměstnanec entit na základě ID e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **PartitionKey**, "empid_" a "email_" umožňují identifikovat index, který chcete použít pro dotaz.  

![Primární a sekundární indexem](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Následující dvě kritéria filtru, která (jeden vyhledávání podle ID zaměstnance a jeden vyhledávání pomocí e-mailové adresy) i zadejte bod dotazy:  

* $filter = (PartitionKey eq ' empid_Sales') a (RowKey eq '000223')
* $filter = (PartitionKey eq ' email_Sales') a (RowKey eq 'jonesj@contoso.com')  

Pokud vyhledat rozsahu entit zaměstnanců, můžete zadat rozsah ID řazení zaměstnanců nebo rozsahu seřazeny v e-mailovou adresu pořadí pomocí dotazu pro entity v příslušnou předponu **RowKey**.  

* Najít všechny zaměstnance z oddělení prodeje s ID zaměstnance v rozsahu **000100** k **000199** seřazeny ve zaměstnanec ID pořadí použití: $filter = (PartitionKey eq ' empid_Sales') a (RowKey ge '000100') a (RowKey le '000199')  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, který začíná slovem "a" seřazených podle e-mailovou adresu pořadí použití: $filter = (PartitionKey eq ' email_Sales') a (RowKey ge "a") a (RowKey lt "b")  

Všimněte si, že se používá ve výše uvedených příkladech syntaxe filtru je ze služby Table rozhraní REST API, další informace najdete v tématu [dotazu entity](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Můžete ponechat duplicitní položky nakonec byl konzistentní mezi sebou pomocí [nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern) udržovat entity primární a sekundární index.  
* Table storage je relativně levný abyste nároky na náklady na ukládání duplicitních dat nesmí být závažný problém. Však měli vždy vyhodnoceny náklady návrhu na základě požadavků vaší předpokládaného úložiště a přidat pouze duplicitní entity, které podporují dotazy, které budou spuštěny klientské aplikace.  
* Hodnota použitá **RowKey** musí být jedinečný pro každé entity. Zvažte použití hodnoty složeného klíče.  
* Odsazení číselných hodnot v **RowKey** (například zaměstnanci ID 000223), umožňuje opravte řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například pokud dotazy tento vyhledávací entity pomocí e-mailu adres v **RowKey** nikdy nepotřebují stáří zaměstnance, tyto entit může mít následující strukturu:
  
   ![Zaměstnanec entity (sekundární index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Je obvykle lepší uložit duplicitní data a ujistěte se, že můžete načíst všechna data, která je nutné se jeden dotaz než chcete použijte jeden dotaz a vyhledejte entitu v primární index sekundární index a druhou pro vyhledávání požadovaná data.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, když klientské aplikace potřebuje k načtení entity pomocí různých odlišné klíče, když váš klient potřebuje k načtení entity v jiné pořadí řazení, a tam, kde můžete identifikovat každé entity pomocí různých jedinečné hodnoty. Použít tento vzor, pokud chcete, aby nedošlo k překročení omezení škálovatelnosti oddíl při provádění entity vyhledávání pomocí různými **RowKey** hodnoty.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor Intra-partition sekundární index](#intra-partition-secondary-index-pattern)  
* [Složené klíče vzor](#compound-key-pattern)  
* [Transakce skupiny entity](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Nakonec byl konzistentní transakce vzor
Povolte nakonec byl konzistentní chování mezi hranice oddílů nebo hranice systému úložiště pomocí front Azure.  

### <a name="context-and-problem"></a>Kontext a problém
EGTs povolit jednotlivé transakce mezi více entit, které sdílejí stejný klíč oddílu. Pro výkon a škálovatelnost, můžete rozhodnout pro ukládání entit, které mají požadavky konzistence v samostatné oddíly nebo v samostatné úložiště systému: v takové situaci nelze použít EGTs k zachování konzistence. Například můžete mít požadavek zachování konzistence typu případné mezi:  

* Entity uložené ve dvou různých oddílů ve stejné tabulce v různých tabulek ve v jiným účtům úložiště.  
* Entity uložené ve službě Table a objekt blob uložené ve službě Blob.  
* Entity uložené ve službě Table a soubor v systému souborů.  
* Úložišti entity ve službě Table ještě indexovat pomocí služby Azure Search.  

### <a name="solution"></a>Řešení
Pomocí fronty Azure můžete implementovat řešení, které nabízí konzistence typu případné mezi dva nebo více oddílů nebo úložných systémů.
Pro ilustraci tohoto přístupu, předpokládá, že máte požadavek mohli archivovat starší zaměstnanec entity. Starší zaměstnanec entity jsou zřídka dotaz a mají být vyloučeny z všechny aktivity, které pracují s aktuální zaměstnanci. K implementaci tento požadavek ukládáte aktivní zaměstnance v **aktuální** tabulky a původní zaměstnance v **archivu** tabulky. Archivace zaměstnanec vyžaduje, abyste odstranit entitu z **aktuální** tabulky a přidejte entity, která má **archivu** tabulky, ale nelze použít EGT k provedení těchto dvou operací. Aby nedošlo k ohrožení, který selhání způsobí, že entita, než se objeví v obou nebo ani jedno z těchto tabulek, musí být operace archivování nakonec byl konzistentní. Následující diagram pořadí popisuje kroky v této operaci. Podrobněji se poskytuje pro cesty výjimka v následující text.  

![Řešení Azure fronty](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient zahájí operaci archivu umístěním zprávu na fronty Azure, v tomto příkladu k archivaci zaměstnanec #456. Role pracovního procesu dotazuje fronty pro nové zprávy; v případě, že některou najde, přečte zprávu a zůstanou skrytá kopie ve frontě. Role pracovního procesu vedle načte kopii entita z **aktuální** tabulky, vloží kopii v **archivu** tabulky a poté se odstraní původní z **aktuální** tabulky. Nakonec pokud nedošlo k chybám z předchozích kroků, role pracovního procesu odstraní skryté zprávy z fronty.  

V tomto příkladu krok 4 vloží zaměstnanec do **archivu** tabulky. Zaměstnanec ho přidat do objektu blob ve službě Blob nebo souboru v systému souborů.  

### <a name="recovering-from-failures"></a>Zotavení z chyb
Je důležité, operace v krocích **4** a **5** musí být *idempotent* v případě, že role pracovního procesu je nutné restartovat operaci archivu. Pokud používáte službu tabulky pro krok **4** byste měli používat operace "vložení nebo nahrazení"; pro krok **5** byste měli používat "odstranit, pokud existuje" operace v knihovně klienta, který používáte. Pokud používáte jiné úložiště systému, musíte použít příslušné idempotent operace.  

Pokud role pracovního procesu nedokončí krok **6**, pak po vypršení časového limitu se zpráva zobrazí znovu ve frontě připravené pro roli pracovního procesu se pokuste znovu ji zpracovat. Role pracovního procesu můžete zkontrolovat, kolikrát byl zprávu ve frontě číst a v případě potřeby příznak je zprávu "poškozených" pro zkoumání odesláním do samostatné fronty. Další informace o čtení zprávy fronty a kontrola počtu dequeue najdete v tématu [získat zprávy](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby ze služby Table a Queue jsou přechodné chyby a klientské aplikace by měla obsahovat logiku vhodný opakování a mohli je zpracovat.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje pro izolace transakce. Například může číst klienta **aktuální** a **archivu** tabulky, pokud byl roli pracovního procesu mezi kroky **4** a **5**a zobrazit nekonzistentní zobrazení data. Všimněte si, že data bude konzistentní nakonec.  
* Je nutné zajistit, že jsou kroky 4 a 5 idempotent pro zajištění konzistence typu případné.  
* Řešení můžete škálovat pomocí více front a instance rolí pracovního procesu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud chcete zaručit konzistence typu případné mezi entitami, které existují v různých oddílů nebo tabulky. Můžete rozšířit tento vzor pro zajištění konzistence typu případné pro operace napříč služby Table a služby objektů Blob a dalších Azure úložných zdroje dat jako databázi nebo systému souborů.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entity](#entity-group-transactions)  
* [Sloučení nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud izolace transakce je důležité pro vaše řešení, měli byste zvážit přepracování vaše tabulky, abyste mohli využívat EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Vzor entity indexu
Udržujte index entity umožňující efektivní hledání, které vrátí seznamy entit.  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table automaticky indexuje entity pomocí **PartitionKey** a **RowKey** hodnoty. To umožňuje klientskou aplikaci k načtení entity efektivní použití bodu dotazu. Například pomocí struktura tabulky vidíte níže, klientská aplikace můžete efektivně načíst entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**).  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Pokud chcete také moci načíst seznam zaměstnanec entit na základě hodnoty jiný jedinečný vlastnosti, například jejich poslední název, musíte použít méně efektivní prohledávání oddílu najít odpovídá spíše než pomocí indexu je přímo vyhledat. Je to proto, že služby table neposkytuje sekundární indexy.  

### <a name="solution"></a>Řešení
K povolení vyhledávání podle příjmení s strukturu entity uvedené výše, musíte udržovat seznam ID zaměstnance. Pokud chcete načíst entity Zaměstnanec s konkrétní příjmení, jako je například Petr, musíte nejprve vyhledat seznam ID zaměstnance pro zaměstnance s Petr jako své příjmení a následně načíst tyto entity zaměstnanců. Existují tři hlavní možnosti pro ukládání seznam ID zaměstnanců:  

* Používání úložiště blob.  
* Vytvořte index entity v stejného oddílu jako entity zaměstnanců.  
* Vytvořte index entity v samostatném oddílu nebo tabulky.  

<u>Možnost #1: Použití objektu blob storage</u>  

Pro první možnost vytvoříte objekt blob pro každý jedinečný příjmení a na každý objekt blob úložiště seznam **PartitionKey** (oddělení) a **RowKey** (ID zaměstnance) hodnoty pro zaměstnance, kteří mají tento příjmení. Při přidání nebo odstranění zaměstnanec se ujistěte, že je obsah objektu blob relevantní nakonec byl konzistentní se zaměstnanec entity.  

<u>Možnost #2:</u> vytvořte index entity v stejného oddílu  

Pro druhou možnost použijte index entit, které obsahují následující údaje:  

![Zaměstnanec index entity](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

**EmployeeIDs** vlastnost obsahuje seznam identifikátorů zaměstnanec pro zaměstnance s příjmení uložené v **RowKey**.  

Následující kroky popisují proces, který byste měli postupovat při přidávání nového zaměstnance Pokud používáte druhou možnost. V tomto příkladu přidáváme zaměstnance s Id 000152 a příjmení Petr z oddělení prodeje:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodej" a **RowKey** hodnotu "Petr." Uložte značku ETag tuto entitu použijte v kroku 2.  
2. Vytvoření skupiny transakci entity (tedy dávkovou operaci), která vloží novou entitu zaměstnanec (**PartitionKey** hodnotu "Prodej" a **RowKey** hodnotu "000152") a aktualizuje entitu indexu (**PartitionKey** hodnotu "Prodej" a **RowKey** hodnotu "Petr") tak, že přidáte do seznamu v poli EmployeeIDs nové ID zaměstnance. Další informace o transakcích skupiny entity najdete v tématu [Entity skupiny transakce](#entity-group-transactions).  
3. Pokud se transakce skupiny entity nezdaří z důvodu chyby optimistickou metodu souběžného (někdo jiný právě změnil index entity), budete muset v kroku 1 znovu spustit.  

Můžete podobný postup k odstranění zaměstnanec, pokud používáte druhou možnost. Změna příjmení zaměstnance je trochu složitější, protože budete muset spustit transakci skupiny entity, která aktualizuje tři entity: Entita zaměstnanců, index entity pro původní příjmení a entity index pro nové příjmení. Před provedením jakýchkoli změn, aby bylo možné načíst ETag hodnoty, které pak můžete použít k provedení aktualizací pomocí optimistickou metodu souběžného musí získat každé entity.  

Následující kroky popisují proces, který byste měli postupovat, když potřebujete vyhledat všechny zaměstnance se daný příjmení v oddělení, pokud používáte druhou možnost. V tomto příkladu jsme jsou všechny zaměstnance se příjmení Petr prodejního oddělení vyhledávány:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodej" a **RowKey** hodnotu "Petr."  
2. Analyzovat seznam ID v poli EmployeeIDs zaměstnanců.  
3. Pokud potřebujete další informace o jednotlivých tito zaměstnanci (například jejich e-mailové adresy), načtení jednotlivých entit zaměstnanec pomocí **PartitionKey** hodnotu "Prodej" a **RowKey** hodnoty ze seznamu zaměstnanců, které jste získali v kroku 2.  

<u>Možnost #3:</u> vytvořte index entity v samostatném oddílu nebo tabulky  

Třetí možností použijte index entit, které obsahují následující údaje:  

![Zaměstnanec index entity v samostatném oddílu](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


**EmployeeIDs** vlastnost obsahuje seznam identifikátorů zaměstnanec pro zaměstnance s příjmení uložené v **RowKey**.  

S parametrem třetí nelze použít EGTs můžete zachovat konzistenci, protože index entity, které jsou v samostatném oddílu z entit zaměstnanců. Měli byste zajistit, že index entity, které jsou nakonec byl konzistentní se entity, které zaměstnanec.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje alespoň dva dotazy k načtení odpovídající entity: jeden k dotazování indexu entity k získání seznamu **RowKey** hodnoty a pak dotazy k načtení jednotlivých entit v seznamu.  
* Vzhledem k tomu, že jednotlivé entity má maximální velikost 1 MB, možnost #2 a možnost #3 v řešení předpokládá, že seznam ID zaměstnance pro danou příjmení je nikdy větší než 1 MB. Pokud seznam ID zaměstnance je pravděpodobně být větší než velikost 1 MB, použijte možnost #1 a ukládat data indexu v úložišti objektů blob.  
* Pokud použijete možnost #2 (pomocí EGTs zpracování přidávání a odstraňování zaměstnanci a změna zaměstnance příjmení), musíte zjistit Pokud objem transakcí bude postupovat omezení škálovatelnosti v daném oddílu. Pokud je to tento případ, měli byste zvážit nakonec byl konzistentní řešení (možnost #1 nebo #3), které používá fronty pro zpracování žádosti o aktualizaci a umožní vám k ukládání entit indexu v samostatném oddílu z entit zaměstnanců.  
* Možnost #2 v tomto řešení se předpokládá, že chcete vyhledávat podle příjmení v rámci oddělení: například chcete načíst seznam zaměstnancům příjmení Petr z oddělení prodeje. Pokud chcete být schopni vyhledat všechny zaměstnance se příjmení Petr napříč celou organizaci, použijte možnost #1 nebo možnost #3.
* Můžete implementovat řešení na základě fronty, který doručí konzistence typu případné (najdete v článku [nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern) podrobnosti).  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Pokud chcete vyhledat sady entit pro všechny sdílené složky běžné hodnotu vlastnosti, například všechny zaměstnance se příjmení Petr, použijte tento vzor.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené klíče vzor](#compound-key-pattern)  
* [Nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Transakce skupiny entity](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Vzor denormalization
Kombinování souvisejících dat společně v jedné entity, aby vám umožňuje načíst všechna data, která je nutné pomocí dotazu jediný bod.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle normalizaci dat k odebrání duplicitních výsledkem dotazy, které načtení dat z více tabulek. Pokud jste normalizaci data do tabulek Azure, musíte nastavit více zpátečních cest z klienta na server se načíst související data. Například s struktura tabulky zobrazené níže potřebovat dvě zpátečních cest k načtení podrobností pro oddělení: jednu pro načtení oddělení entita, která zahrnuje správce je ID a pak další žádost o načtení manažera podrobnosti v entitě zaměstnanců.  

![Oddělení entitu a entitu zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Řešení
Místo ukládání dat do dvou samostatných entit, denormalize data a ponechat si kopii manažera podrobnosti v entitě oddělení. Příklad:  

![Oddělení entity](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

S entitami oddělení uložené s těmito vlastnostmi můžete nyní načíst všechny podrobnosti, které je třeba o použití bodu dotazu oddělení.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Není náklady režie spojená s ukládáním některá data dvakrát. Výhody výkonu (vyplývající z méně požadavků na službu úložiště) většinou převáží okrajového vzrůst náklady na úložiště (a náklady na tento je částečně posunut snížení počtu transakcí, které budete potřebovat načíst podrobnosti o oddělení).  
* Konzistence dvě entity, které obsahují informace o správcích musí zachovat. Problém konzistence můžete řešit pomocí EGTs aktualizace více entit v rámci jedné transakce atomic: v takovém případě entity oddělení a zaměstnanci entity pro správce oddělení ukládají do stejného oddílu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete často vyhledat související informace. Tento vzor snižuje počet dotazů, které musíte provést načíst data, která vyžaduje vašeho klienta.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené klíče vzor](#compound-key-pattern)  
* [Transakce skupiny entity](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Složené klíče vzor
Použití složené **RowKey** hodnoty, aby klient k vyhledání souvisejících dat pomocí dotazu jediný bod.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je poměrně přirozené lze pomocí spojení v dotazech vrátit související částí dat klientovi v jednom dotazu. ID zaměstnance můžete například použít k vyhledání seznam entit v relaci, které obsahují výkonu a zkontrolujte data pro zaměstnance.  

Předpokládejme, že zaměstnanec entity ukládáte ve službě Table pomocí následující strukturu:  

![Struktura zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Musíte taky k uložení historická data týkající se recenze a výkon pro každý rok, které zaměstnanec odpracoval pro vaši organizaci a musíte být schopni přistupovat k těmto informacím o rok. Jednou z možností je vytvořit jiné tabulky, která ukládá entity s následující strukturou:  

![Struktura alternativní zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Všimněte si, že s tímto přístupem můžete rozhodnout pro duplicitní některé informace (například křestní jméno a příjmení) v nové entity, která má vám umožní načíst dat pomocí jedné žádosti. Nelze však udržovat silnou konzistenci, protože EGT nejde použít k aktualizaci dvě entity atomicky.  

### <a name="solution"></a>Řešení
Uložte nový typ entity v původní tabulky pomocí entity s následující strukturou:  

![Řešení pro strukturu zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Všimněte si jak **RowKey** je nyní složený klíč skládá z ID zaměstnance a rok zkontrolujte data, která vám umožňuje načíst zaměstnance výkonu a zkontrolujte data pomocí jedné žádosti pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data revize pro konkrétního zaměstnance (například zaměstnanci 000123 z oddělení prodeje):  

$filter = (PartitionKey eq 'Prodej') a (RowKey ge 'empid_000123') a (RowKey lt 'empid_000124') & $select = RowKey, Manager hodnocení, sdílené hodnocení a komentáře  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který umožňuje snadno rozložit **RowKey** hodnota: například **000123_2012**.  
* Tuto entitu se také ukládání do stejného oddílu jako jiné entity, které obsahují data v relaci pro stejné zaměstnance, což znamená, že EGTs můžete použít k udržování silnou konzistenci.
* Měli byste zvážit, jak často bude dotaz na data k určení, zda je tento vzor vhodná.  Například pokud bude mít přístup zřídka zkontrolujte data a data hlavní zaměstnance často byste měli mít je jako samostatné entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud je třeba uložit jeden nebo více související entity dotazu často.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entity](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  
* [Nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Vzor protokolu poškozené databáze
Načtení *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí zpětné datum a čas pořadí.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je možné načíst nedávno vytvořených entity, například nejnovější deset výdaje odeslaný zaměstnanec deklarací identity. Tabulka dotazuje podporu **$top** dotaz operace vrátit první *n* entit ze sady: neprobíhá žádná operace ekvivalentní dotaz vrátit poslední n entity v sadě.  

### <a name="solution"></a>Řešení
Ukládání entit, použití **RowKey** že přirozeně seřadí v pořadí zpětné datum a čas pomocí tak poslední položka je vždy první z nich v tabulce.  

Abyste mohli vyhledat deset nejnovější deklarace výdajů odeslaný zaměstnanec, například můžete použít reverzní značek hodnotou odvozenou od aktuální datum a čas. Jeden způsob, jak vytvořit vhodný hodnota "obrácený rysky" pro znázorňuje následující ukázka kódu C# **RowKey** , seřadí z nejnovější k nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Můžete získat zpět na hodnotu data a času pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Dotaz tabulky vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Musí odsadí zpětné značek hodnotu s úvodní nuly zajistit, že řetězcovou hodnotu seřadí podle očekávání.  
* Musíte být vědomi škálovatelnost cílů na úrovni oddílu. Dávejte pozor, vytváření oddílů aktivního bodu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud budete potřebovat pro přístup k entity v pořadí zpětné datum a čas nebo když potřebujete přístup k nedávno přidané entity.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřadit / append proti vzor](#prepend-append-anti-pattern)  
* [Načtení entit](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Vzor velkému odstranění
Povolit odstranění k velkému počtu entity uložením všechny entity pro souběžné odstranění vlastní samostatné tabulky; Odstranění entity odstraněním v tabulce.  

### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstranit stará data, která už musí být k dispozici pro klientské aplikace, nebo že aplikace má archivovat na jiné úložné médium. Obvykle identifikovat taková data data: například máte požadavek odstranit záznamy všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jeden návrhu možné je použít datum a čas přihlášení na požadavek na **RowKey**:  

![Datum a čas pokus o přihlášení](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Tento přístup zabraňuje hotspotům oddílu, protože aplikace můžete vložit a odstranit entity přihlášení pro každého uživatele v samostatném oddílu. Tento postup však může být nákladná a časově náročné Pokud máte velký počet entit, protože nejdřív je potřeba provést prohledávání tabulky za účelem zjištění všech entit odstranit a pak je nutné odstranit každé staré entity. Všimněte si, že můžete snížit počet zpátečních cest k serveru potřeba dávkování více žádosti o odstranění do EGTs odstraňte starý entity.  

### <a name="solution"></a>Řešení
Do samostatné tabulky použijte pro každý den pokusů o přihlášení. Výše uvedený návrh entit můžete vyhnout hotspotům při vkládání entity a odstranění starší entity je nyní prostě odstranění jedna tabulka každý den (jedno úložiště operace) namísto hledání a odstraňování stovky a tisíce jednotlivých přihlášení entity každý den.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje váš návrh další způsoby, které vaše aplikace bude používat data, jako je například vyhledávání konkrétních entit, propojení s jinými data nebo informace o generování agregační?  
* Návrhu vyhnout aktivní body při vkládání nové entity  
* Pokud chcete po odstranění ho znovu použít stejný název tabulky očekávají, že ke zpoždění. Je lepší vždy nutné použít názvy jedinečné tabulky.  
* Očekávají, že některé omezení při prvním použití nové tabulky při služby Table zjišťuje přístupové vzorce a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často budete muset vytvořit nové tabulky.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, když máte k velkému počtu entit, které je nutné odstranit ve stejnou dobu.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entity](#entity-group-transactions)
* [Úprava entity](#modifying-entities)  

## <a name="data-series-pattern"></a>Řada vzorek dat
Dokončení datové řady úložiště v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

### <a name="context-and-problem"></a>Kontext a problém
Obvyklým scénářem je pro aplikace pro uložení řadu data, která je obvykle potřeba načíst všechny najednou. Aplikace může například záznam kolik zasílání Rychlých zpráv každý zaměstnanec odešle každou hodinu a pak tyto informace použít k vykreslení kolik zpráv každý uživatel, odešlou přes předchozích 24 hodin. Pro uložení 24 entity pro každý zaměstnanec může být jeden návrhu:  

![Uložení 24 entity pro každý zaměstnanec](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

V tomto návrhu můžete snadno vyhledat a aktualizovat entity, která má aktualizace pro každý zaměstnanec vždy, když aplikace potřebuje k aktualizaci hodnota počtu zpráv. K načtení informací k vykreslení grafu aktivity předchozích 24 hodin, ale musí získat 24 entity.  

### <a name="solution"></a>Řešení
Použijte následující návrh s samostatné vlastností pro uložení počet zpráv pro každou hodinu:  

![Zpráva statistiky entity](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

V tomto návrhu můžete aktualizovat počet zpráv pro zaměstnance pro zadané hodiny operace sloučení. Teď můžete načíst všechny informace, které potřebujete k vykreslení grafu pomocí žádosti pro jednu entitu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud dokončení datové řady nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložišti například objekt blob.  
* Pokud máte víc klientů současně aktualizaci entity, budete muset použít **značka ETag** implementovat optimistickou metodu souběžného. Pokud máte mnoho klientů, můžete se setkat vysoké kolizí.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud je potřeba aktualizovat a načíst data řady přidružené jednotlivých entit.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučení nebo nahradit](#merge-or-replace)  
* [Nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern) (Pokud ukládáte datové řady do objektu BLOB)  

## <a name="wide-entities-pattern"></a>Vzor široké entity
Slouží k ukládání logických entit s více než 252 vlastností více fyzických entit.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivých entit může mít více než 252 vlastností (s výjimkou vlastnosti povinné systému) a nelze uložit více než 1 MB dat celkem. V relační databázi by obvykle získáte zaokrouhlí žádné omezení velikosti řádku přidáním nové tabulky a vynucování relace 1: 1 mezi nimi.  

### <a name="solution"></a>Řešení
Pomocí služby Table, můžete uložit více entit představují objekt jeden velký podnik s více než 252 vlastností. Například pokud chcete uložit počet Rychlých zpráv odeslaných každý zaměstnanec za posledních 365 dnů, můžete použít následující návrh, který používá dvě entity s různými schématy:  

![Více entit](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obě entity k jejich synchronizovány mezi sebou můžete použít EGT. Jinak operace sloučení jednoho můžete aktualizovat počet zpráv pro určitý den. Načíst všechna data pro jednotlivé zaměstnance musí načíst obě entity, které můžete provést dva efektivní požadavky, které používají oba **PartitionKey** a **RowKey** hodnotu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Načítání dokončení logická entita zahrnuje alespoň dva transakce úložiště: jeden pro načtení jednotlivých fyzická entita.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Použijte tento vzor, kdy je nutné uložit entity, jejichž velikost nebo počet vlastností přesahuje omezení pro jednotlivé entity ve službě Table.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakce skupiny entity](#entity-group-transactions)
* [Sloučení nebo nahradit](#merge-or-replace)

## <a name="large-entities-pattern"></a>Vzor velkých entit
Použijte úložiště objektů blob k ukládání velkých vlastnost hodnoty.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity Nejde uložit více než 1 MB dat celkem. Pokud jeden nebo několik vlastnosti ukládá hodnoty, které způsobí celková velikost vaší entity, která má být vyšší než tato hodnota, nemůžete uložit celý entity služby Table.  

### <a name="solution"></a>Řešení
Pokud vaší entity překračuje 1 MB velikost, protože jeden nebo více vlastností obsahovat velké množství dat, můžete ukládat data ve službě Blob a potom uložte adresu objektu blob ve vlastnosti v entitě. Například můžete ukládat fotografie zaměstnanec v úložišti objektů blob a uložte odkaz na fotografii v **fotografií** vlastnosti vaší entity zaměstnanců:  

![Vlastnost fotografii](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* K zajištění konzistence typu případné mezi entity ve službě Table a data ve službě Blob, použít [nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern) zachování vaší entity.
* Načítání úplnou entitu zahrnuje alespoň dva transakce úložiště: jeden pro načtení entity a jeden pro načtení dat objektů blob.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Pokud budete potřebovat k ukládání entit, jehož velikost překračuje omezení pro jednotlivé entity ve službě Table, použijte tento vzor.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec byl konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor široké entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Předřazení připojit proti vzor
Zvýšení škálovatelnosti, když máte k velkému počtu vloží tak, že se vložení napříč více oddílů.  

### <a name="context-and-problem"></a>Kontext a problém
Předřazení nebo připojování entity na vaše uložené entity obvykle výsledkem přidání nové entity na první nebo poslední oddíl posloupnosti oddíly aplikace. V takovém případě všechny vložení v každém okamžiku dala ve stejném oddílu, vytváření aktivní oblast, která zabraňuje služby table z zatížení vyrovnávání vložení mezi několika uzly a což může způsobit vaše aplikace a stiskněte tlačítko cíle škálovatelnosti pro oddíl. Například pokud máte aplikaci, která protokoly sítě a přístupu k prostředkům zaměstnanci, pak entity strukturu jak je uvedeno níže mohly by vést do aktuální hodiny oddílu stane aktivní oblast, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddílu:  

![Struktura entity](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Řešení
Následující strukturu alternativní entity zabraňuje v žádné konkrétní oddílu aktivní bod jako protokoly událostí aplikace:  

![Struktura alternativní entity](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Všimněte si tento příklad jak oba **PartitionKey** a **RowKey** jsou složeného klíče. **PartitionKey** využívá ID oddělení i zaměstnanců k distribuci protokolování napříč více oddílů.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje alternativní klíče struktura, která zabraňuje vytváření aktivní oddíly na vložení efektivní dotazy, které provede klientské aplikace?  
* Předpokládaného svazku transakcí znamená, že budete chtít nejspíš k dosažení cíle škálovatelnosti pro jednotlivé oddíl a omezeny službou úložiště?  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Připojit/prepend proti vzor Vyhněte se při svazku transakcí je pravděpodobné, aby výsledkem omezení pomocí služby úložiště při přístupu k aktivní oddíl.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené klíče vzor](#compound-key-pattern)  
* [Vzor protokolu poškozené databáze](#log-tail-pattern)  
* [Úprava entity](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Proti vzorek dat protokolu
Místo služby Table by měl obvykle používat službu objektů Blob k ukládání dat protokolu.  

### <a name="context-and-problem"></a>Kontext a problém
Případ použití běžné pro data protokolu je načíst výběr položky protokolu pro konkrétní datum a čas rozsah: například chcete najít všechny chyby a kritické zprávy, které vaše aplikace protokolují mezi 15:04 a 15:06 na konkrétní datum. Nechcete použít k určení oddílu uložit protokolu entity k datum a čas zprávy protokolu: který výsledkem aktivního oddílu, protože v každém okamžiku budou sdílet všechny entity služby protokolu stejné **PartitionKey** hodnotu (naleznete v části [Prepend/připojovat proti vzor](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu dochází v oddílu aktivní, protože aplikace pro aktuální datum a hodinu všechny zprávy protokolu zapíše do oddílu:  

![Entity zpráv protokolu](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

V tomto příkladu **RowKey** obsahuje datum a čas zprávy protokolu zajistit, že zprávy protokolu jsou uloženy řazení datum a čas a obsahuje ID zprávy v případě, že více zpráv protokolu sdílet stejné datum a čas.  

Další možností je použít **PartitionKey** zajistí, že aplikace zapíše zprávy napříč celou řadu oddíly. Například pokud zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy napříč mnoha oddílů, můžete použít následující schéma entity:  

![Alternativní protokolu zpráv entity](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problém s tímto schématem je však načíst všechny zprávy protokolu pro konkrétní časové rozpětí musí prohledávat každý oddíl v tabulce.

### <a name="solution"></a>Řešení
V předchozí části zvýrazněná problém pokusu o použití služby Table pro uložení položek protokolu a navrhované dva, nevyhovující, návrhy. Jedno řešení, která vedla k aktivní oddíl s riziko nízký výkon zápis zpráv protokolu; jiné řešení výsledkem dotaz nízký výkon z důvodu požadavek na kontrolovat každý oddíl v tabulce k načtení protokolu zprávy pro konkrétní časové období. Úložiště BLOB nabízí lepší řešení pro tento typ scénáře, a to je, jak Azure Storage Analytics ukládá data protokolu shromažďuje.  

Tato část popisuje, jak analytika úložiště ukládá data protokolu v úložišti objektů blob jako ilustraci tohoto přístupu k ukládání dat, která obvykle dotazování podle rozsahu.  

Analytika úložiště ukládá zprávy protokolu ve formátu odděleného do více objektů BLOB. Formát odděleného usnadňuje klientskou aplikaci k analýze dat ve zprávě protokolu.  

Analytika úložiště používá vytváření názvů pro objekty BLOB, které umožňuje vyhledat objekt blob (nebo objekty BLOB), která obsahují zprávy protokolu, pro které chcete najít. Například objekt blob s názvem "queue/2014/07/31/1800/000001.log" obsahuje zprávy protokolu, které se vztahují ke službě fronty za hodinu od 18:00 na 31 červenec 2014. "000001" označuje, že toto je první soubor protokolu pro toto období. Analytika úložiště taky zaznamenává časová razítka zpráv protokolu první a poslední uložené v souboru jako součást metadata objektu blob. Rozhraní API pro objekt blob úložiště umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu: Chcete-li vyhledat všechny objekty BLOB, které obsahují data protokolu fronty za hodinu od 18:00, můžete použít předponu "fronty/2014/07/31/1800."  

Vyrovnávací paměti Analytics úložiště interně protokolování zpráv a pravidelně aktualizuje odpovídající objekt blob nebo vytvoří nový s nejnovější dávku položky protokolu. Tím se snižuje počet zápisů, které musíte provést na služby objektů blob.  

Při implementaci řešení podobné ve vaší vlastní aplikaci, musíte zvážit, jakým způsobem spravovat kompromis mezi spolehlivost (zápis každá položka protokolu do úložiště objektů blob jako Odehrává se) a náklady a škálovatelnost (ukládání do vyrovnávací paměti aktualizací ve vaší aplikaci a jejich zápis do úložiště objektů blob v dávkách).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o tom, jak ukládat data protokolu, zvažte následující body:  

* Pokud vytvoříte návrh tabulky, který zabraňuje potenciální aktivní oddíly, můžete zjistit, data protokolu nelze efektivní přístup.  
* Ke zpracování dat protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když je často strukturovaná data protokolu, úložiště objektů blob může být lepším řešením.  

## <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé aspekty k berte v úvahu při implementaci vzory popsané v předchozích částech. Většina Tato část používá příklady napsané v C#, které používají knihovnu klienta služby Storage (verze 4.3.0 v době psaní textu).  

## <a name="retrieving-entities"></a>Načtení entit
Jak je popsáno v části [návrhu pro dotazování](#design-for-querying), nejvíce efektivní dotaz je dotaz bodu. Ale v některých scénářích musíte k načtení více entit. Tato část popisuje některé běžné přístupy k načtení entity pomocí klientské knihovny pro úložiště.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Spuštění dotazu na bodu pomocí klientské knihovny pro úložiště
Nejjednodušší způsob, jak provést dotaz bod je použití **načíst** tabulky operace, jak je znázorněno následujícím C# fragment kódu, který načte entity s **PartitionKey** hodnoty "Prodej" a **RowKey** hodnoty "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává entity ho načte být typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Načítání více entit pomocí LINQ
Můžete načíst pomocí LINQ s Klientská knihovna pro úložiště a zadat dotaz s více entit **kde** klauzule. Abyste se vyhnuli prohledávání tabulky, by měla vždycky obsahovat **PartitionKey** hodnotu v where klauzule a pokud je to možné **RowKey** hodnotu, aby se zabránilo prohledávání tabulky a oddíl. Služba table podporuje omezenou sadu operátory porovnání (větší než, větší než nebo rovna méně než je menší než nebo rovna, stejné a není rovno) pro použití v where klauzule. Následující fragment kódu jazyka C# vyhledá všechny zaměstnance, jejichž poslední název začíná na "B" (za předpokladu, že **RowKey** ukládá příjmení) v oddělení prodeje (za předpokladu, že **PartitionKey** ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Všimněte si, jak dotaz Určuje, jak **RowKey** a **PartitionKey** zajistit lepší výkon.  

Následující příklad kódu ukazuje ekvivalentní funkce pomocí rozhraní fluent API (Další informace o rozhraní fluent API obecně platí, najdete v části [osvědčené postupy pro navrhování rozhraní Fluent API](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Ukázka vnořena více **CombineFilters** metody pro zahrnutí tři filtr podmínek.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Načítání velký počet entit z dotazu
Optimální dotaz vrátí jednotlivých entit na základě **PartitionKey** hodnotu a **RowKey** hodnotu. Ale v některých případech může mít požadavek na vrácení mnoho entit ze stejného oddílu nebo dokonce z mnoha oddílů.  

V takových případech by měla vždy plně testování výkonu aplikace.  

Dotazy na data služby table může vrátit maximálně 1000 entit najednou a může spustit maximálně pět sekund. Pokud sadu výsledků dotazu obsahuje více než 1 000 entity, pokud dotaz nebyl dokončen v pět sekund, nebo pokud dotaz protne hranice oddílu, služby Table vrátí token pokračování povolit klientskou aplikaci požádat o další sadu entit. Další informace o tom, jak pokračování tokeny pracovní najdete v tématu [časový limit dotazu a Paginování](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte Klientská knihovna pro úložiště, může ho automaticky jako vrátí entity ze služby Table pro vás zpracovávat pokračování tokeny. Následující C# ukázka kódu pomocí klientské knihovny pro úložiště automaticky zpracovává pokračování tokeny, pokud je služba table vrátí v odpovědi:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Následující kód C# zpracovává pokračování tokeny explicitně:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Pomocí explicitně pokračování tokenů, můžete řídit, kdy aplikace načítá další segment dat. Například pokud klientské aplikace umožňuje uživatelům procházet entity, které jsou uložené v tabulce, uživatel může rozhodnout všechny entity načíst dotaz tak, že vaše aplikace by token pokračování používat pouze pro načtení další segment, když uživatel měl dokončí stránkování prostřednictvím všechny entity v aktuálním segmentu procházet po stránkách. Tento přístup má několik výhod:  

* Umožňuje omezit množství dat načíst ze služby Table a že přesouváte přes síť.  
* Umožňuje provádět asynchronní vstupně-výstupní operace v rozhraní .NET.  
* Umožňuje serializovat token pokračování do trvalého úložiště, takže můžete pokračovat v případě při selhání aplikace.  

> [!NOTE]
> Token pokračování obvykle vrátí segment obsahující 1000 entity, i když může být méně. Toto se taky případě, když je omezit počet položek dotaz vrátí pomocí **trvat** vrátit první n entitami, které odpovídají zadaným kritériím vyhledávání: služby table může vrátit segment obsahující méně než n entity společně s token pokračování umožnit načtení zbývající entit.  
> 
> 

Následující kód C# ukazuje, jak upravit počet entit, vrátila uvnitř segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít maximálně 255 vlastnosti a mít velikost až 1 MB. Při dotazování tabulky a načtení entit, možná nebudete potřebovat všechny vlastnosti a přenosu dat zbytečně (Pokud chcete snížit latenci a náklady) se můžete vyhnout. Projekce na straně serveru může použít k přenosu pouze vlastnosti, které potřebujete. V následujícím příkladu se načte jenom na **e-mailu** vlastnost (spolu s **PartitionKey**, **RowKey**, **časové razítko**, a **značka ETag**) z entit vybrána v dotazu.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Všimněte si jak **RowKey** hodnota je k dispozici, přestože nebyl uveden v seznamu vlastností pro načtení.  

## <a name="modifying-entities"></a>Úprava entity
Klientská knihovna pro úložiště umožňuje upravit vaší entity uložené ve službě table vkládání, odstraňování a aktualizaci entity. Můžete EGTs dávky více insert, update a operace odstranění společně a snížit počet zpátečních cest vyžaduje a zlepšíte výkon vašeho řešení.  

Všimněte si, že výjimky vydané když klientská knihovna pro úložiště provede EGT obvykle zahrnují index entity, která způsobila, že dávka selhání. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh ovlivňuje způsob, jakým klientské aplikace zpracovává operace souběžnosti a aktualizace.  

### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení, implementuje optimistickou metodu souběžného zpracování kontroluje na úrovni jednotlivých entit pro služby table **vložit**, **sloučení**, a **odstranit** operace, přestože je možné pro klienta, chcete-li vynutit služby table obejít těchto kontrol. Další informace o správě služby table souběžnosti najdete v tématu [Správa souběžnost v Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Sloučení nebo nahradit
**Nahradit** metodu **TableOperation** třída vždycky nahradí úplnou entitu ve službě Table. Pokud nezadáte vlastnost v žádosti o tuto vlastnost existuje v uložené entity, žádost tuto vlastnost odebere uložené entity. Pokud chcete explicitně odebrání vlastnosti z uložené entity, musí obsahovat všech vlastností v požadavku.  

Můžete použít **sloučení** metodu **TableOperation** třídy ke snížení objemu dat, která odešlete do služby Table, pokud chcete aktualizovat entitu. **Sloučení** metoda nahradí všechny vlastnosti v entitě uložené hodnoty vlastností z entity, které jsou součástí požadavku, ale zůstanou zachovány vlastnosti uložené entity, které nejsou zahrnuté v požadavku. To je užitečné, pokud máte velké entity a potřeba jenom aktualizovat malý počet vlastností v požadavku.  

> [!NOTE]
> **Nahradit** a **sloučení** metody nezdaří, pokud entita neexistuje. Jako alternativu, můžete použít **InsertOrReplace** a **InsertOrMerge** metody, které vytvářejí nové entity, pokud neexistuje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Práce s typy heterogenní entit
Služba Table je *bez schématu* tabulky úložiště, která znamená, že na jednotlivé tabulky můžete ukládat entity více typů poskytuje flexibilitu při návrhu. Následující příklad ilustruje tabulku ukládání zaměstnanců a entity oddělení:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Časové razítko</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Název oddělení</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Všimněte si, že každá entita musí mít dál **PartitionKey**, **RowKey**, a **časové razítko** hodnoty, ale mohou mít všechny sadu vlastností. Kromě toho není nic k označení typu entity, pokud se rozhodnete ukládat někde tyto informace. Existují dvě možnosti pro identifikaci typ entity:  

* Předřadit typ entity, který má **RowKey** (nebo případně **PartitionKey**). Například **EMPLOYEE_000123** nebo **DEPARTMENT_SALES** jako **RowKey** hodnoty.  
* Použijte samostatné vlastnost k zaznamenání typ entity, jak je znázorněno v následující tabulce.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Časové razítko</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ entity</th>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ entity</th>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ entity</th>
<th>Název oddělení</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Oddělení</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ entity</th>
<th>FirstName</th>
<th>Příjmení</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnanec</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

První možnost předřazení entity typ, který má **RowKey**, je užitečné, pokud je možné, že dvě entity různých typů může mít stejnou hodnotu klíče. Skupiny také entity stejného typu společně v oddílu.  

Technik popsaných v této části jsou obzvláště důležité v diskusi [vztahy dědičnosti](table-storage-design-modeling.md#inheritance-relationships) výše v tomto průvodci v článku [modelování vztahů](table-storage-design-modeling.md).  

> [!NOTE]
> Měli byste zvážit, včetně číslo verze v hodnotě typ entity povolit klientské aplikace momentální objektů POCO a pracovat s různými verzemi.  
> 
> 

Zbývající část Tato část popisuje některé z funkcí v Klientská knihovna pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

### <a name="retrieving-heterogeneous-entity-types"></a>Načítání typy heterogenní entit
Pokud používáte Klientská knihovna pro úložiště, máte tři možnosti pro práci s více typy entit.  

Pokud znáte typ entity s konkrétní **RowKey** a **PartitionKey** hodnoty, potom můžete zadat typ entity při načtení entity, jak je uvedeno v předchozí dva příklady, které načtení entity typu **EmployeeEntity**: [provádění dotazu bodu pomocí klientské knihovny pro úložiště](#executing-a-point-query-using-the-storage-client-library) a [načítání více entit pomocí LINQ](#retrieving-multiple-entities-using-linq).  

Druhou možností je používat **DynamicTableEntity** typu (kontejner objektů) namísto konkrétní typ entity objektů POCO (Tato možnost může také zvýšit výkon, protože není nutné k serializaci a deserializaci entity, která má typy .NET). Následující kód C# potenciálně načte více entit různých typů z tabulky, ale vrací všechny entity jako **DynamicTableEntity** instance. Poté použije **EntityType** vlastnosti k určení typu Každá entita:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Všimněte si, že pro načtení další vlastnosti, je nutné použít **TryGetValue** metodu **vlastnosti** vlastnost **DynamicTableEntity** – třída.  

Třetí možnost je kombinovat pomocí **DynamicTableEntity** typu a **EntityResolver** instance. Díky tomu můžete použít k řešení pro více typů objektů POCO ve stejném dotazu. V tomto příkladu **EntityResolver** delegáta je pomocí **EntityType** vlastnost k rozlišení mezi těmito dvěma typy entit, který vrací dotaz. **Vyřešit** metoda používá **překladač** delegáta vyřešit **DynamicTableEntity** instance na **TableEntity** instance.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Úprava typy heterogenní entit
Není potřeba znát typ entity ho odstranit a při vložení vždy znát typ entity. Můžete však použít **DynamicTableEntity** typu chcete entitu aktualizovat, aniž by věděly, její typ a bez použití třídu entity objektů POCO. Následující ukázka kódu načte jednu entitu a zkontroluje **EmployeeCount** existuje vlastnost před aktualizací.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

## <a name="controlling-access-with-shared-access-signatures"></a>Řízení přístupu s podpisy sdíleného přístupu
Tokeny sdíleného přístupového podpisu (SAS) můžete povolit klientské aplikace upravit (a dotazovat) entity tabulky přímo, aniž by bylo nutné přímé ověření u služby table. Obvykle jsou tři hlavní výhody použití SAS ve vaší aplikaci:  

* Není nutné distribuovat klíč účtu úložiště do nezabezpečené platformy (například mobilní zařízení) Chcete-li povolit toto zařízení k přístupu a úpravě entity ve službě Table.  
* Snižování zátěže některé úkoly, které provádějí webové a pracovní role ve správě vaší entity do klientských zařízení, jako je například počítačích koncových uživatelů a mobilních zařízení.  
* Můžete přiřadit omezené a čas omezenou sadu oprávnění pro klienta (například umožníte přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o používání tokeny SAS s služby Table najdete v tématu [pomocí sdíleného přístupového podpisy (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Však musí i nadále generovat tokeny SAS, která udělují klientskou aplikaci pro entity ve službě table: bude třeba provést v prostředí, které má zabezpečený přístup k klíče účtu úložiště. Obvykle použijete roli web nebo worker generovat tokeny SAS a doručit do klientských aplikací, které potřebují přístup k vaší entity. Protože je stále režii zahrnutých v generování a doručování tokeny SAS pro klienty, zvažte, jak nejlépe snížit tento režijní náklady, zejména v vysoký počet scénáře.  

Je možné k vygenerování tokenu SAS, která uděluje přístup k podmnožině entity v tabulce. Ve výchozím nastavení, můžete vytvořit token SAS pro celou tabulku, ale je také možné určit, že tokenu SAS udělit přístup k buď řadu **PartitionKey** hodnoty nebo celou řadu **PartitionKey** a **RowKey** hodnoty. Můžete zvolit generovat tokeny SAS pro jednotlivé uživatele systému tak, aby každý uživatel tokenu SAS pouze jim umožňuje přístup k vlastní entity ve službě table.  

## <a name="asynchronous-and-parallel-operations"></a>Operace asynchronní a paralelní
Zadaný své žádosti jsou šíření mezi více oddílů, můžete zlepšit propustnost a klient odezvy pomocí asynchronní nebo paralelní dotazy.
Například můžete mít dvě nebo více pracovních instance rolí přístup k vaší tabulky paralelně. Můžete mít role jednotlivým pracovním zodpovědná za konkrétní sady oddíly, nebo jednoduše mít více instancí služby worker role, každý mít přístup všechny oddíly v tabulce.  

V rámci instanci klienta můžete zlepšit propustnost asynchronně provádění operace úložiště. Klientská knihovna pro úložiště snadno zápisu asynchronní dotazy a úpravy. Například můžete začít s synchronní metoda, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu jazyka C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Tento kód můžete snadno upravit tak, aby dotaz asynchronně následujícím způsobem:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

V tomto příkladu asynchronní, můžete zjistit z synchronní verze následující změny:  

* Podpis metody nyní zahrnuje **asynchronní** modifikátor a vrátí **úloh** instance.  
* Místo volání **ExecuteSegmented** metoda a načtěte výsledky, metoda nyní volá **ExecuteSegmentedAsync** metoda a používá **await** modifikátor a načtěte výsledky asynchronně.  

Klientská aplikace lze tuto metodu volat vícekrát (s různými hodnotami **oddělení** parametr), a každý dotaz se spustí na samostatné vlákno.  

Všimněte si, že žádné asynchronní verzi **Execute** metoda v **TableQuery** třídy, protože **rozhraní IEnumerable** rozhraní nepodporuje asynchronní výčtu.  

Můžete také vložit, aktualizovat a odstranit entity asynchronně. Následující příklad jazyka C# ukazuje jednoduchý, synchronní metoda vložení nebo nahrazení entity zaměstnanců:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Tento kód můžete snadno upravit tak, aby aktualizaci asynchronně následujícím způsobem:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto příkladu asynchronní, můžete zjistit z synchronní verze následující změny:  

* Podpis metody nyní zahrnuje **asynchronní** modifikátor a vrátí **úloh** instance.  
* Místo volání **Execute** metoda aktualizovat entitu, metoda nyní volá **ExecuteAsync** metoda a používá **await** modifikátor a načtěte výsledky asynchronně.  

Klientská aplikace můžete volat více asynchronní metody, jako je tato, a každé vyvolání metody se spustí na samostatné vlákno.  

## <a name="next-steps"></a>Další postup

- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat v tabulce](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)