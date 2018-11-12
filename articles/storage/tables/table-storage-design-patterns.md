---
title: Vzory návrhu v tabulce Azure storage | Dokumentace Microsoftu
description: Použití vzorů pro řešení Azure table service.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: d055ea9b30732e1cc0fc4ae5471bae26adc08b35
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238892"
---
# <a name="table-design-patterns"></a>Způsoby návrhu tabulek
Tento článek popisuje některé vzory, které jsou vhodné pro použití s řešeními služby tabulky. Uvidíte také, jak se prakticky vyřešit některé problémy a kompromisy popsané v dalších článcích návrh tabulky úložiště. Následující diagram obsahuje souhrn vztahy mezi různé vzorce:  

![k vyhledání souvisejících dat](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Vzorek mapy nad zvýrazní některých relací mezi (modrá) modely a antimodely (oranžová), které jsou popsané v této příručce. Existuje mnoho vzorků, které stojí za zvážení. Například jeden z klíčových scénářů pro službu Table Service je použít [model Materializovaného zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [příkaz oddělení pro odpovědnost dotazů (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) vzor.  

## <a name="intra-partition-secondary-index-pattern"></a>Model sekundárních indexů uvnitř oddílu
Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) umožňují rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty. Aktualizace mezi kopie může udržovat konzistentní vzhledem k aplikacím pomocí EGT společnosti.  

### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky je uvedeno níže, klientské aplikace pomocí bodu dotaz k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle ID zaměstnance v rámci každé oddělení.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Pokud chcete také moct vyhledat entitu zaměstnanci založena na hodnotě jiné vlastnosti, jako jsou e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu pro vyhledání shody. Je to proto, že služba table service neposkytuje sekundární indexy. Kromě toho není žádná možnost žádost o seznam zaměstnanců řazení v jiném pořadí než **RowKey** pořadí.  

### <a name="solution"></a>Řešení
Obejít chybějící sekundární indexy, můžete uložit několik kopií Každá entita se každá kopie použitím různých **RowKey** hodnotu. Pokud ukládáte entity s struktury je uvedeno níže, můžete efektivně načítat zaměstnance entity na základě ID e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **RowKey**, "empid_" a "email_" umožňují dotazování jednoho zaměstnance nebo rozsah zaměstnanců pomocí celé řady e-mailové adresy nebo ID zaměstnance.  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Následující kritéria filtru dva (jeden vyhledávání podle ID zaměstnance a jeden vyhledávání pomocí e-mailová adresa) zadejte oba bodových dotazů:  

* $filter = (PartitionKey eq "Prodeje") a (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Prodeje") a (RowKey eq 'email_jonesj@contoso.com")  

Když odešlete dotaz rozsahu entit zaměstnanců, můžete zadat rozsah seřazené podle ID zaměstnance, nebo rozsah seřazené podle e-mailové adresy pomocí dotazu pro entity příslušnou předponu v **RowKey**.  

* Najít všechny zaměstnance z oddělení prodeje se zaměstnanec ID v rozsahu 000100 k 000199 použití: $filter = (PartitionKey eq "Prodeje") a (RowKey ge "empid_000100") a (RowKey le "empid_000199")  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, začíná písmenem "a" použití: $filter = (PartitionKey eq "Prodeje") a (RowKey ge "email_a") a (RowKey lt "email_b")  
  
  Všimněte si, že je filtr syntaxe používané ve výše uvedených příkladech z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Table storage je relativně levné, abyste mohli režii náklady na uložení duplicitních dat nesmí být závažný problém. By ale vždy vyhodnoceny náklady na návrhu na základě vašich požadavků na vytížení úložiště a pouze přidat duplicitní entity na podporu dotazů, které se spustí klientské aplikace.  
* Protože sekundární index entity, které se ukládají do stejného oddílu jako původní entity, měli byste zajistit nepřekročí cíle škálovatelnosti pro jednotlivé oddíl.  
* Abyste mohli duplicitní položky konzistentní mezi sebou pomocí EGTs atomicky aktualizovat dvě kopie entity. Z toho vyplývá, že byste měli uložit všechny kopie entity do stejného oddílu. Další informace najdete v části [pomocí transakcí skupin entit](table-storage-design.md#entity-group-transactions).  
* Hodnota použitá **RowKey** musí být jedinečný pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Odsazení číselných hodnot v **RowKey** (například ID 000223 zaměstnance), opravte umožňuje řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například pokud dotazy tento vyhledávací entity pomocí e-mailu adresa v **RowKey** nikdy nepotřebují zaměstnance věk, může tyto entity mají následující strukturu:

   ![Struktury entit zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Je obvykle vhodnější ukládat duplicitní data a ujistěte se, že můžete načíst všechna data pomocí jediného dotazu a než chcete použijte jednoho dotazu a vyhledejte entitu a druhý pro vyhledání požadovaná data.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když klientská aplikace potřebuje k načtení entit s využitím širokou škálu různých klíčů, když klient potřebuje načítání entit na jiné pořadí řazení, a pokud chcete zjistit, každá entita, využít celou řadu jedinečné hodnoty. Nicméně byste měli jistotu, že nedošlo k překročení limitů škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých **RowKey** hodnoty.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Model mezi oddílu sekundárních indexů](#inter-partition-secondary-index-pattern)
* [Složené sekvence klíče](#compound-key-pattern)
* [Transakcí skupin entit](#entity-group-transactions)
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Model mezi oddílu sekundárních indexů
Store několik kopií každého entitu s využitím různých **RowKey** hodnoty v samostatných oddílů nebo v samostatné tabulky, které umožňují rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey**hodnoty.  

### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí těchto hodnot. Například struktura tabulky je uvedeno níže, klientské aplikace pomocí bodu dotaz k načtení entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**  hodnoty). Klienta můžete také načíst entity seřazené podle ID zaměstnance v rámci každé oddělení.  

![ID zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Pokud chcete také moct vyhledat entitu zaměstnanci založena na hodnotě jiné vlastnosti, jako jsou e-mailovou adresu, musíte použít méně efektivní prohledávání oddílu pro vyhledání shody. Je to proto, že služba table service neposkytuje sekundární indexy. Kromě toho není žádná možnost žádost o seznam zaměstnanců řazení v jiném pořadí než **RowKey** pořadí.  

Jsou předvídání velmi velký objem transakcí proti takové instituci a chcete minimalizovat riziko omezení klienta služby Table service.  

### <a name="solution"></a>Řešení
Obejít chybějící sekundární indexy, můžete uložit několik kopií Každá entita s každou kopii pomocí různých **PartitionKey** a **RowKey** hodnoty. Pokud ukládáte entity s struktury je uvedeno níže, můžete efektivně načítat zaměstnance entity na základě ID e-mailovou adresu nebo zaměstnanců. Předpona hodnoty **PartitionKey**, "empid_" a "email_" umožňují určit index, který chcete použít pro dotaz.  

![Index primárního a sekundárního indexu](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Následující kritéria filtru dva (jeden vyhledávání podle ID zaměstnance a jeden vyhledávání pomocí e-mailová adresa) zadejte oba bodových dotazů:  

* $filter = (PartitionKey eq ' empid_Sales") a (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") a (RowKey eq 'jonesj@contoso.com")  

Když odešlete dotaz rozsahu entit zaměstnanců, můžete zadat rozsah seřazené podle ID zaměstnance, nebo rozsah seřazené podle e-mailové adresy pomocí dotazu pro entity příslušnou předponu v **RowKey**.  

* Najít všechny zaměstnance z oddělení prodeje s ID zaměstnance v rozsahu **000100** k **000199** seřazeny vzestupně v zaměstnance ID pořadí použití: $filter = (PartitionKey eq ' empid_Sales") a (RowKey ge"000100") a (RowKey le "000199")  
* Najít všechny zaměstnance z oddělení prodeje s e-mailovou adresu, která začíná textem "a" v e-mailovou adresu pořadí použijte seřazený: $filter = (PartitionKey eq ' email_Sales") a (RowKey ge"a") a (RowKey lt"b")  

Všimněte si, že je filtr syntaxe používané ve výše uvedených příkladech z rozhraní REST API služby tabulky pro další informace najdete v tématu [dotazu entity](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Můžete ponechat duplicitní položky konzistentní mezi sebou pomocí [konečnou konzistenci transakcí vzor](#eventually-consistent-transactions-pattern) udržovat entity primárních a sekundárních indexů.  
* Table storage je relativně levné, abyste mohli režii náklady na uložení duplicitních dat nesmí být závažný problém. By ale vždy vyhodnoceny náklady na návrhu na základě vašich požadavků na vytížení úložiště a pouze přidat duplicitní entity na podporu dotazů, které se spustí klientské aplikace.  
* Hodnota použitá **RowKey** musí být jedinečný pro každou entitu. Zvažte použití složené klíčové hodnoty.  
* Odsazení číselných hodnot v **RowKey** (například ID 000223 zaměstnance), opravte umožňuje řazení a filtrování na základě horní a dolní meze.  
* Nutně není potřeba duplicitní vlastnosti vaší entity. Například pokud dotazy tento vyhledávací entity pomocí e-mailu adresa v **RowKey** nikdy nepotřebují zaměstnance věk, může tyto entity mají následující strukturu:
  
   ![Zaměstnanec entity (sekundární index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Je obvykle vhodnější duplicitními daty a ujistěte se, že můžete načíst všechna data, která potřebujete, s pomocí jediného dotazu než chcete použijte jednoho dotazu a vyhledejte entitu pomocí sekundární index a druhou pro vyhledávání požadovaných dat v primární index.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když klientská aplikace potřebuje k načtení entit s využitím širokou škálu různých klíčů, když klient potřebuje načítání entit na jiné pořadí řazení, a pokud chcete zjistit, každá entita, využít celou řadu jedinečné hodnoty. Tento model použijte, pokud chcete, aby nedošlo k překročení limitů škálovatelnosti oddílu jsou při vyhledávání entit pomocí různých **RowKey** hodnoty.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Model sekundárních indexů uvnitř oddílu](#intra-partition-secondary-index-pattern)  
* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Vzor konečnou konzistenci transakcí
Pomocí front Azure umožňuje konzistentní chování napříč hranice oddílů nebo systému hranice úložiště.  

### <a name="context-and-problem"></a>Kontext a problém
EGTs umožňuje atomické transakce napříč více entit, které sdílejí stejný klíč oddílu. Pro výkon a škálovatelnost, můžete se rozhodnout pro uložení entity, které mají na konzistenci v samostatných oddílech nebo v samostatné úložiště systému: v takové situaci nelze použít EGTs můžete zachovat konzistenci. Například můžete mít povinnost udržovat konečnou konzistenci mezi:  

* Entity uložená ve dvou různých oddílů ve stejné tabulce, různých tabulek nebo jiný účet úložiště.  
* Na entitu uloženou ve službě Table a blob uložených ve službě Blob service.  
* Na entitu uloženou ve službě Table service a soubor v systému souborů.  
* Úložiště entity ve službě Table service dosud indexovány pomocí služby Azure Search.  

### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které zajišťuje konečnou konzistenci napříč dvěma nebo více oddílů nebo úložných systémů.
Pro ilustraci tento přístup předpokládá, že máte požadavek mohli archivovat starší zaměstnance entity. Starší zaměstnance entity jsou zřídka dotazovat a má být vyloučen z jakékoli činnosti, které pracují s aktuální zaměstnanci. Chcete-li implementovat tento požadavek můžete ukládat aktivních zaměstnanců v **aktuální** tabulky a staré zaměstnanci v **archivu** tabulky. Archivace zaměstnanec je potřeba odstranit entitu z **aktuální** tabulku a přidat entita, která má **archivu** tabulky, ale nelze použít EGT provádět tyto dvě operace. Aby nevznikalo riziko, způsobující selhání entity se zobrazí v obou nebo ani jedno z těchto tabulek, musí být operace archivování konečnou konzistenci. Následující sekvence diagramu popisuje kroky v této operaci. Podrobněji se poskytuje pro cest výjimek. v následujícím textu.  

![Řešení fronty Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient spustí operaci archivu tak, že zprávu ve frontě Azure, v tomto příkladu pro archivaci zaměstnance #456. Role pracovního procesu dotazuje fronty na nové zprávy. Po nalezení znaku jednu, přečte zprávu a ponechá skrytá kopie ve frontě. Role pracovního procesu načítá další kopie entity z **aktuální** tabulky, vloží kopii **archivu** tabulku a pak odstraní původní z **aktuální** tabulky. Nakonec pokud nebyly zjištěny žádné chyby v předchozích krocích, role pracovního procesu odstraní skrytou zprávu z fronty.  

Krok 4 v tomto příkladu vloží zaměstnance do **archivu** tabulky. Zaměstnanec ho přidat do souboru v systému souborů nebo objekt blob ve službě Blob service.  

### <a name="recovering-from-failures"></a>Zotavení z chyby
Je důležité, které operace v krocích **4** a **5** musí být *idempotentní* v případě, že role pracovního procesu je nutné restartovat archivní operace. Pokud používáte služby Table service pro krok **4** byste měli použít operace "Vložit nebo nahradit"; krok **5** byste měli používat "odstranit, pokud existuje" operace v knihovně klienta, který používáte. Pokud používáte jiný úložný systém, musíte použít odpovídající idempotentní operace.  

Pokud role pracovního procesu nikdy dokončení kroku **6**, pak po vypršení časového limitu se zpráva zobrazí znovu ve frontě připravené pro roli pracovního procesu se pokuste ji znovu zpracovat. Role pracovního procesu můžete zkontrolovat, kolikrát zprávu ve frontě byla čtení a v případě potřeby příznak je "nezpracovatelná" zpráva pro šetření a odeslat ho do samostatné fronty. Další informace o čtení zprávy fronty a kontrola počet odstranění z fronty, naleznete v tématu [získání zpráv](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby z tabulky a fronty služby jsou přechodné chyby a klientské aplikace by měla obsahovat logiku opakování vhodné jejich zpracování.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje pro transakci izolace. Například může klienta číst **aktuální** a **archivu** tabulky při role pracovního procesu se mezi jednotlivými kroky **4** a **5**a podívejte se nekonzistentní zobrazení dat. Všimněte si, že data budou konzistentní nakonec.  
* Je nutné zajistit, že kroky 4 a 5 jsou idempotentní, aby bylo možné zajistit konzistenci typu případné.  
* Řešení můžete škálovat s použitím více front a instancí rolí pracovního procesu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete zajistit konečnou konzistenci mezi entitami, které existují v různých oddílů nebo tabulky. Tento model zajistit konzistenci typu případné pro operace ve službě Table service a službu Blob service a jiné než Azure úložiště zdroje dat, jako jsou databáze nebo systému souborů můžete rozšířit.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)  
* [Sloučení nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Transakce izolace je důležité, abyste vaše řešení, měli byste zvážit, realizace vám umožní používat EGTs tabulek.  
> 
> 

## <a name="index-entities-pattern"></a>Model entity indexů
Udržujte index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  

### <a name="context-and-problem"></a>Kontext a problém
Automaticky indexuje entit s využitím služby Table service **PartitionKey** a **RowKey** hodnoty. To umožňuje klientské aplikaci k načtení entity efektivně pomocí dotazu bodu. Například pomocí struktura tabulky je uvedeno níže, klientské aplikace můžou efektivně načítat entity jednotlivých zaměstnanců pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**).  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Pokud chcete také moct načíst seznam entit zaměstnance založena na hodnotě jiné vlastnosti není jedinečný, jako je například poslední název, musíte použít méně efektivní prohledávání oddílu najít odpovídá místo použití indexu je vyhledat přímo. Je to proto, že služba table service neposkytuje sekundární indexy.  

### <a name="solution"></a>Řešení
Povolit vyhledávání podle příjmení pomocí struktury entit, které jsou uvedené výše, musíte mít seznam ID zaměstnance. Pokud budete chtít získat entity zaměstnance poslední název, jako je například Jones, musí nejprve vyhledat seznam ID zaměstnance pro zaměstnance s Jones jako jejich příjmení a následně načíst tyto entity zaměstnance. Existují tři hlavní možnosti pro ukládání seznamů ID zaměstnance:  

* Používání úložiště blob.  
* Vytvořte index entity do stejného oddílu jako entity zaměstnance.  
* Vytvoření indexu entit v samostatném oddílu nebo tabulky.  

<u>Možnost #1: Úložiště objektů blob můžete použít</u>  

Pro první možnost se vám vytvoření objektu blob pro každou jedinečnou příjmení a v každé úložiště objektů blob v seznamu **PartitionKey** (department) a **RowKey** (ID zaměstnance) hodnoty pro zaměstnance, kteří mají tento poslední název. Při přidání nebo odstranění zaměstnanci měli byste zajistit, že obsah objektu blob relevantní je konzistentní s entitami zaměstnance.  

<u>Možnost #2:</u> vytvořit index entity do stejného oddílu  

Pro druhou možnost použijte index entity, které ukládat následující data:  

![Zaměstnanec index entity](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

**EmployeeIDs** vlastnost obsahuje seznam ID zaměstnance pro zaměstnance s poslední název uložený v **RowKey**.  

Následující kroky popisují proces, kterým byste měli postupovat při přidávání nového zaměstnance Pokud používáte druhou možnost. V tomto příkladu přidáváme zaměstnance s Id 000152 a příjmení Jones prodejního oddělení:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Jones." Uložte ETag tuto entitu pro použití v kroku 2.  
2. Vytvoření skupiny transakce entity (to znamená, dávkové operace), která vloží nové zaměstnance entity (**PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "000152") a aktualizuje index entity (**PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Novák") tak, že přidáte do seznamu v poli EmployeeIDs nové ID zaměstnance. Další informace o transakcí skupin entit najdete v tématu [transakcí skupin entit](#entity-group-transactions).  
3. Pokud transakce skupiny entit selže z důvodu chyby optimistického řízení souběžnosti (někdo právě změnil index entity), budete muset začít v kroku 1.  

Podobný přístup se odstraňuje zaměstnanec, pokud používáte druhou možnost můžete použít. Změna zaměstnance příjmení je o něco složitější, protože budete muset provést transakci skupiny entity, která aktualizuje entity tři: entity zaměstnance, index entity pro staré příjmení a index entitu pro nové příjmení. Každá entita musí získat před provedením jakýchkoli změn získat značku ETag hodnoty, které pak můžete provádět aktualizace pomocí optimistického řízení souběžnosti.  

Následující kroky popisují proces, kterým byste měli postupovat, když budete chtít vyhledat všechny zaměstnance se daný příjmení v oddělení, pokud používáte druhou možnost. V tomto příkladu jsme se všichni zaměstnanci s příjmení Jones prodejního oddělení vyhledávání:  

1. Načtení entity index s **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnotu "Jones."  
2. Parsovat seznam ID v poli EmployeeIDs zaměstnanců.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanci (například jejich e-mailové adresy), načíst všechny entity zaměstnance pomocí **PartitionKey** hodnotu "Prodeje" a **RowKey** hodnoty z seznam zaměstnanců, které jste získali v kroku 2.  

<u>Možnost #3:</u> vytvořit index entity v samostatném oddílu nebo tabulky  

Třetí možnost použití indexu entity, které ukládat následující data:  

![Zaměstnanec index entity v samostatném oddílu](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


**EmployeeIDs** vlastnost obsahuje seznam ID zaměstnance pro zaměstnance s poslední název uložený v **RowKey**.  

Pomocí třetí možnost nelze použít EGTs můžete zachovat konzistenci, protože index entity, které jsou v samostatném oddílu z entity, které zaměstnanec. Měli byste zajistit, že entity indexu jsou konzistentní s entitami zaměstnance.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje alespoň dva dotazy pro načtení odpovídajících entit: jednu k dotazování indexu entity, které chcete získat seznam **RowKey** hodnoty a potom dotazy k načtení jednotlivých entit v seznamu.  
* Vzhledem k tomu, že jednotlivé entity má maximální velikost 1 MB, možnost #2 a #3 v řešení předpokládá, že seznam ID zaměstnance pro danou příjmení je nikdy větší než 1 MB. Pokud seznam ID zaměstnance bude pravděpodobně do 1 MB přesahovat, použijte možnost #1 a ukládat data indexu ve službě blob storage.  
* Pokud použijete možnost #2 (pomocí EGTs zpracování přidávání a odstraňování zaměstnanci a změna zaměstnance příjmení) naplňují Pokud objem transakcí se přístup omezení škálovatelnosti v daném oddílu. Pokud je to tento případ, měli byste zvážit konzistentní řešení typu (možnost #1 nebo #3), která používá fronty pro zpracování žádosti o aktualizaci a umožňuje ukládání entit indexu v samostatném oddílu z entit zaměstnance.  
* Možnost #2 v tomto řešení se předpokládá, že chcete vyhledat podle jména v rámci oddělení: například chcete načíst seznam zaměstnancům příjmení Jones prodejního oddělení. Pokud chcete vyhledat všechny zaměstnance se příjmení Jones v celé organizaci, použijte buď možnost #1 nebo #3.
* Můžete implementovat řešení na základě fronty, která poskytuje konečnou konzistenci (najdete v článku [vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern) další podrobnosti).  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete vyhledat sadu entit, které sdílejí společné hodnoty vlastnosti, jako je například všechny zaměstnance se příjmení Jones.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Vzor denormalizace
Kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, které potřebujete, s jediným bodem dotazu.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle normalizovat data odebrat duplicity, které jsou výsledkem dotazů, které načítají data z více tabulek. Pokud jste normalizovat data do tabulek Azure, musíte udělat více výměn od klienta k serveru a související data načíst. Například s struktura tabulky níže budete potřebovat dva zpátečních cest k načtení podrobností pro oddělení: z nich se má načíst oddělení entity, která zahrnují správce uživatele, ID a potom další požadavek k načtení podrobností manažera v entitě zaměstnance.  

![Oddělení entitou a entitou zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Řešení
Místo ukládání dat v rámci dvě samostatné entity, denormalizovat data a ponechat si kopii manažera podrobnosti v entitě oddělení. Příklad:  

![Oddělení entity](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

S entitami oddělení uložená s těmito vlastnostmi můžete teď načíst všechny podrobnosti, potřebné informace o oddělení pomocí dotazu bodu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Existuje nějaké náklady režie spojené s ukládáním některá data dvakrát. Výhody výkonu (vyplývající z menší počet požadavků na službu storage) obvykle větší váhu než minimálním ziskem zvýšení nákladů na úložiště (a snížení počtu transakcí, které budete potřebovat k načtení podrobností oddělení částečně posunut tyto náklady ).  
* Konzistence dvě entity, které ukládají informace o správcích, musíte mít. Problém konzistence může zpracovávat pomocí EGTs aktualizovat několik entit v jediné atomické transakce: v tomto případě oddělení entitu a entitu zaměstnanci pro vedoucí oddělení se ukládají do stejného oddílu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když je často potřeba vyhledat související informace. Tento model snižuje počet dotazů, které váš klient musí Ujistěte se, aby se načetla data, které vyžaduje.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Složené sekvence klíče
Použití složeného **RowKey** hodnoty umožňují klientům vyhledat související data pomocí dotazu jediný bod.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je poměrně přirozené použít spojení v dotazech, který vrátí související časti dat klientovi v jediném dotazu. ID zaměstnance můžete například použít k vyhledání seznam související entity, které obsahují výkon a kontrolovat data pro zaměstnance.  

Předpokládejme, že ukládáte zaměstnance entit ve službě Table service pomocí následující struktury:  

![Struktury entit zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Musíte také ukládat historická data týkající se kontroly a výkonu pro jednotlivé roky, které zaměstnanec pracoval pro vaši organizaci, a musíte být schopni přistupovat k těmto informacím podle roku. Jednou z možností je vytvoření další tabulky, která ukládá entity s následující strukturou:  

![Struktury entit alternativní zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Všimněte si, že s tímto přístupem budete muset duplikovat některé informace (například křestní jméno a příjmení) v nové entity umožňující vám umožní načíst dat s využitím jedné žádosti. Nelze však udržovat silnou konzistenci, protože EGT nelze použít k aktualizaci těchto dvou entitách atomicky.  

### <a name="solution"></a>Řešení
Nový typ entity Store v původní tabulce pomocí entit s následující strukturou:  

![Řešení pro zaměstnance struktury entit](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Všimněte si, že jak **RowKey** je nyní složený klíč skládá z ID zaměstnance a rok revize data, která umožňuje načíst zaměstnance výkon a kontrolovat data jedním požadavkem pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data kontroly pro zaměstnance (třeba 000123 zaměstnanci z oddělení prodeje):  

$filter = (PartitionKey eq "Prodeje") a (RowKey ge "empid_000123") a (RowKey lt 'empid_000124') & $select = RowKey, správce hodnocení, Peer hodnocení, komentáře  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Používejte vhodný oddělovací znak, který usnadňuje parse **RowKey** hodnota: například **000123_2012**.  
* Tato entita jsou také ukládání do stejného oddílu jako další entity, které obsahují související data pro stejné zaměstnance, což znamená, že vám pomůže EGTs udržovat silnou konzistenci.
* Měli byste zvážit, jak často se zadávat dotazy na data k určení, zda tento vzor odpovídá.  Například pokud budete využívat data recenzí zřídka a daty o zaměstnancích hlavní často je nutné jej uschovat jako samostatné entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete ukládat jeden nebo více souvisejících entit dotazu často.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)  
* [Práce s typy heterogenní entit](#working-with-heterogeneous-entity-types)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Vzor log tail
Načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je, bude moct načíst nedávno vytvořené entity, třeba posledních deset výdaje odeslal zaměstnanec deklarací identity. Dotazy podpory **$top** dotazové operace do vrátí první *n* entity ze sady: neexistuje žádná odpovídající dotaz operace vrátit poslední n entity v sadě.  

### <a name="solution"></a>Řešení
Store entity pomocí **RowKey** přirozeně seřadí v pořadí reverzní datum a čas pomocí tak nejnovější položky je vždy první z nich v tabulce.  

Například aby bylo možné načíst deset nejnovější deklarací výdajů odeslal zaměstnanec, můžete použít reverzní značek hodnotou odvozenou od aktuálního data a času. Následující vzorový kód jazyka C# ukazuje jeden ze způsobů vytvoření vhodnou hodnotu "obrácený značky" pro **RowKey** , která řadí od nejnovější do nejstaršího:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Můžete získat zpět na hodnotu data a času pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabulka dotaz vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Musíte vyplnění zpětného značek hodnotu s úvodní nuly Ujistěte se, že hodnotu řetězce seřadí podle očekávání.  
* Musíte být vědomi cíle škálovatelnosti na úrovni oddílu. Buďte opatrní vytváření oddílů aktivního bodu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když budete potřebovat pro přístup k entity v pořadí reverzní data a času nebo když potřebujete pro přístup k naposledy přidané entity.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřaďte / připojovat proti vzor](#prepend-append-anti-pattern)  
* [Načítání entit](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Vzor vysoké objemy delete
Povolit odstranění k velkému počtu entit uložením všechny entity pro souběžné odstranění vlastních samostatné tabulky; odstranit entity odstraněním tabulky.  

### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstranit stará data, která už musí být k dispozici pro klientské aplikace, nebo aplikace má archivovat do jiné úložné médium. Obvykle identifikaci těchto dat pomocí datum: například máte požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jeden je to možné je použít datum a čas žádosti o přihlášení v **RowKey**:  

![Datum a čas pokus o přihlášení](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Tento přístup vyhnete hotspotům oddílu, protože aplikace může vložení a odstranění entit přihlášení pro každého uživatele v samostatném oddílu. Tento přístup však může být drahá a časově náročné Pokud máte velké množství entit, protože nejdřív je potřeba provést prohledávání tabulky za účelem zjištění všech entity, které chcete odstranit, a pak musíte odstranit každé staré entity. Všimněte si, že můžete snížit počet zpátečních cest k serveru muset odstranit staré entity do EGTs dávkování víc požadavků delete.  

### <a name="solution"></a>Řešení
Do samostatné tabulky použijte pro každý den pokusů o přihlášení. Výše uvedené entity návrhu můžete použít při vkládání entit a odstraňování starých entity je nyní jednoduše dotaz odstranění jedné tabulky každý den, aby hotspotům (úložiště jediné operace) namísto hledání a odstraňování stovkami a tisíci osoba entity přihlášení každý den.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje váš návrh dalších způsobů, jak bude aplikace používat data, jako je například vyhledávání konkrétních entit, propojení s jinými dat nebo generování souhrnné informace?  
* Váš návrh vyhnout aktivní body při vkládání nové entity?  
* Pokud chcete znovu použít stejný název tabulky po jejím odstranění očekávat, že ke zpoždění. Je lepší, vždy používali unikátní tabulku názvů.  
* Můžete očekávejte některé omezení při prvním použití novou tabulku služby Table service učí vzorce přístupu a distribuuje oddílů mezi uzly. Měli byste zvážit, jak často je potřeba vytvořit nové tabulky.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že máte velký počet entit, které je nutné odstranit ve stejnou dobu.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)
* [Úprava entit](#modifying-entities)  

## <a name="data-series-pattern"></a>Vzor data řady
Řada kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

### <a name="context-and-problem"></a>Kontext a problém
Běžný scénář, kdy je aplikace k ukládání řadu data, která je obvykle potřeba načíst všechny najednou. Vaše aplikace například může zaznamenat kolik zasílání Rychlých zpráv každý zaměstnanec odešle každou hodinu a pak tyto informace slouží k vykreslení počet zpráv každý uživatel odesílá přes předchozími 24 hodinami. Jeden návrhu může být k ukládání 24 entity pro každý zaměstnanec:  

![Pro každý zaměstnanec Store 24 entity](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

V tomto návrhu může snadno najít a aktualizovat se pro každý zaměstnanec pokaždé, když aplikace potřebuje aktualizovat hodnota počtu zpráv. K načtení informací k vykreslení grafu aktivity za předchozích 24 hodin, ale musíte načíst 24 entity.  

### <a name="solution"></a>Řešení
K uložení počet zpráv pro každou hodinu pomocí samostatných vlastnost následující:  

![Entity statistiky zpráv](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Operace sloučení s tímto návrhem slouží k aktualizaci počet zpráv pro zaměstnance jenom konkrétní hodiny. Nyní můžete načíst všechny informace potřebné k vykreslení grafu pomocí žádosti pro jednu entitu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud dokončení datové řady se nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště třeba jako objekt blob.  
* Pokud máte víc klientů současně aktualizaci entity, je potřeba použít **ETag** implementace optimistického řízení souběžnosti. Pokud máte mnoho klientů, může docházet vysokou kolize.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když potřebujete aktualizovat a načíst datové řady přidružené jednotlivých entit.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučení nebo nahradit](#merge-or-replace)  
* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern) (Pokud ukládáte datové řady do objektu BLOB)  

## <a name="wide-entities-pattern"></a>Vzor široké entity
Slouží k ukládání logické entity s více než 252 vlastností více fyzických entit.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivých entit může mít maximálně 252 vlastností (s výjimkou vlastnosti povinné systému) a nejde uložit více než 1 MB dat celkem. V relační databázi by získáte zpravidla round žádné omezení velikosti řádku přidat novou tabulku a vynucování vztah 1: 1 mezi nimi.  

### <a name="solution"></a>Řešení
Použití služby Table service, můžete uložit více entity, které představují jeden velký podnik objekt s více než 252 vlastností. Například pokud chcete uložit počet Rychlých zpráv odesílaných jednotliví zaměstnanci za posledních 365 dnů, můžete použít následující návrhu, který používá dvě entity s různými schématy:  

![Více entit](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Pokud je potřeba provést změnu, která vyžaduje aktualizaci obě entity k zachování pro synchronizaci mezi sebou můžete použít EGT. V opačném případě můžete použít jeden sloučených aktualizovat počet zpráv pro určitý den. Načíst všechna data pro jednotlivé zaměstnance musí načíst obě entity, které vám pomůžou s dva efektivní požadavky, které obě používají **PartitionKey** a **RowKey** hodnotu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Načítání kompletní logická entita vyžaduje alespoň dva transakce služby storage: jednu k načtení každá fyzická entita.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě potřeba ukládat entity, jejichž velikost nebo počet vlastností překročí limity pro jednotlivé entity ve službě Table service.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Transakcí skupin entit](#entity-group-transactions)
* [Sloučení nebo nahradit](#merge-or-replace)

## <a name="large-entities-pattern"></a>Vzor velkých entit
Používání úložiště blob k ukládání hodnot vlastností velké.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity Nejde uložit více než 1 MB dat celkem. Pokud jeden nebo několik z vlastností ukládání hodnot, které způsobují celková velikost překročí tuto hodnotu vaší entity, nelze ukládat celé entity ve službě Table service.  

### <a name="solution"></a>Řešení
Pokud entita 1 MB překračuje velikost protože jednu nebo více vlastností obsahovat velké množství dat, můžete ukládat data ve službě Blob service a potom ukládání adresy objektu blob ve vlastnosti v entitě. Například můžete ukládat fotky zaměstnanec v úložišti objektů blob a ukládání odkaz fotografii v **fotografii** vlastnosti vaší entity zaměstnance:  

![Vlastnost fotografií](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zachovat konečné konzistenci mezi entity ve službě Table service a data ve službě Blob service, použijte [konečnou konzistenci transakcí vzor](#eventually-consistent-transactions-pattern) udržovat vaše entity.
* Načítání úplnou entitu zahrnuje nejméně dva transakce služby storage: jednu k načtení entity a z nich se má načíst data objektů blob.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, když budete chtít ukládat entity, jejichž velikost přesahuje limity pro jednotlivé entity ve službě Table service.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor konečnou konzistenci transakcí](#eventually-consistent-transactions-pattern)  
* [Vzor široké entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Předřaďte/připojovat proti vzor
Až budete mít k velkému počtu vloží tím, že rozprostírá vložení informací napříč několika oddíly, zvýšení škálovatelnosti.  

### <a name="context-and-problem"></a>Kontext a problém
Předřazení nebo připojením entit k uložené entity obvykle za následek přidání nové entity na první nebo poslední oddíl posloupnost oddíly aplikace. V takovém případě všechny operace vložení v daném okamžiku je možné umístit do stejného oddílu, vytváření, který brání službě table service z vyrovnávání zatížení vkládání napříč několika uzly a pravděpodobně způsobuje aplikaci k dosažení cíle škálovatelnosti pro aktivní bod oddíl. Například pokud máte aplikaci, která zaměstnanci přistupovat k protokoly sítě a prostředků, pak struktury entit, jak je znázorněno níže může způsobit oddílu do aktuální hodiny, stane hotspot, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivé oddíl:  

![Struktury entit](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Řešení
Následující strukturu alternativní entity se vyhnete aktivní bod na žádný konkrétní oddíl jako protokoly událostí aplikace:  

![Struktura alternativní entity](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Všimněte si, že v tomto příkladu jak i **PartitionKey** a **RowKey** jsou složené klíče. **PartitionKey** ID oddělení a zaměstnanců používá k distribuci protokolování napříč několika oddíly.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Alternativní klíče struktura, která zabraňuje vytváření aktivních oddílů na vloží efektivně podporuje dotazy, které klientská aplikace odešle?  
* Váš předpokládaný objemu transakcí znamená, že budete pravděpodobně k dosažení cíle škálovatelnosti pro jednotlivé oddíl a omezí službou storage?  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Připojení/prepend proti vzor vyhněte, když objem transakcí by mohla způsobit omezení šířky pásma službou storage při přístupu k horkými oddíly.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Složené sekvence klíče](#compound-key-pattern)  
* [Vzor log tail](#log-tail-pattern)  
* [Úprava entit](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Ochrana proti vzorek dat protokolu
Obvykle byste měli použít službu Blob service namísto služby Table service k ukládání dat protokolu.  

### <a name="context-and-problem"></a>Kontext a problém
Běžný případ použití pro data protokolu, je načtení výběru položky protokolu pro konkrétní datum a čas rozsah: například chcete najít všechny chyby a kritické zprávy, které aplikace protokoluje mezi 15:04 a 15:06 v konkrétní den. Nechcete použít k určení oddílu, uložte protokol entity na datum a čas zprávy protokolu:, který vede horkými oddíly, protože v daném okamžiku se všechny entity protokolu sdílet stejný **PartitionKey** hodnotu (viz část [Prepend/připojovat proti vzor](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu za následek horkými oddíly vzhledem k tomu, že aplikace zapíše všechny zprávy protokolu do oddílu pro aktuální datum a hodiny:  

![Entity zpráv protokolu](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

V tomto příkladu **RowKey** obsahuje datum a čas zprávy protokolu k zajištění, že zprávy protokolu jsou uloženy seřazeny vzestupně v pořadí datum a čas a ID zprávy v případě, že více zpráv protokolu sdílet stejný datum a čas.  

Další možností je použít **PartitionKey** , což zajišťuje, že aplikace zapíše zpráv mezi širokou škálou oddíly. Například pokud zdroj zprávy protokolu zajišťuje distribuci zpráv do mnoho oddílů, můžete použít následující schéma entity:  

![Entity alternativní protokolu zpráv](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problém s tímto schématem je však načíst všechny zprávy protokolu pro konkrétní časové období musí prohledávat každý oddíl v tabulce.

### <a name="solution"></a>Řešení
V předchozí části zvýrazněné problém pokusu o použití služby Table service k ukládání položek protokolu a navrhované dvou, nevyhovující, návrhy. Jedno řešení, které vedly k horkými oddíly s rizikem nízký výkon zápis zprávy protokolu. Dalším řešením výsledkem dotazu nízký výkon díky zadanému požadavku kontrolovat každý oddíl v tabulce k načtení zprávy protokolu pro konkrétní časové období. BLOB storage nabízí lepší řešení pro takové scénáře a toto je Azure Storage Analytics ukládá data protokolu shromažďuje.  

Tato část popisuje, jak Storage Analytics ukládá data protokolu ve službě blob storage jako ilustraci tohoto přístupu k ukládání dat, která se obvykle dotazujete na rozsah.  

Analýza úložiště ukládá zprávy protokolu ve formátu s oddělovači v víc objektů BLOB. Formát odděleného usnadňuje klientské aplikaci parsovat data ve zprávě protokolu.  

Analýza úložiště používá zásady vytváření názvů pro objekty BLOB, které umožňuje vyhledat objekt blob (nebo objekty BLOB), které obsahují zprávy protokolu, které hledáte. Například objekt blob s názvem "queue/2014/07/31/1800/000001.log" obsahuje zprávy protokolu, které se vztahují ke službě fronty za hodinu od 18:00 do 31. července 2014. "000001" označuje, že toto je první soubor protokolu pro toto období. Analýza úložiště taky zaznamenává časová razítka první a poslední protokolové zprávy, uloženy v souboru metadat objektu blob v rámci. Rozhraní API pro objekt blob úložiště umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu: Chcete-li vyhledat všechny objekty BLOB, které obsahují data protokolu fronty za hodinu od 18:00, můžete použít předponu "fronty/2014/07/31/1800."  

Vyrovnávací paměti úložišť Analytics interně protokolování zpráv a potom pravidelně aktualizuje odpovídající objekt blob nebo vytvoří nový s poslední dávku položky protokolu. To snižuje počet zápisů, které musíte provést na službu blob service.  

Pokud implementujete podobné řešení ve své aplikaci, musíte zvážit způsob správy kompromis mezi spolehlivosti (zápis každá položka protokolu do úložiště objektů blob v jejím průběhu) a náklady a škálovatelnost (ukládání do vyrovnávací paměti aktualizace pro vaše aplikace a zápis je do úložiště objektů blob v dávkách).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o tom, jak ukládat data protokolu, zvažte následující body:  

* Pokud vytvoříte návrh tabulky, které se vyhýbají potenciální aktivních oddílů, může být pro vás data protokolu nelze efektivní přístup.  
* Ke zpracování dat protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když je často strukturovaná data protokolu, úložiště objektů blob může být lepším řešením.  

## <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé důležité informace k berte v úvahu při implementaci vzorce popsané v předchozích částech. Většinu této části se používají příklady napsané v jazyce C#, které použijte klientskou knihovnu pro úložiště (verze 4.3.0 v době psaní).  

## <a name="retrieving-entities"></a>Načítání entit
Jak je popsáno v části [návrhu pro dotazování](#design-for-querying), efektivní dotaz je dotaz bodu. Nicméně v některých případech budete muset načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit s využitím klientskou knihovnu pro úložiště.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Provádění dotazu bodu pomocí klientskou knihovnu pro úložiště
Nejjednodušší způsob, jak provést dotaz bod je použít **načíst** operace tabulky, jak je znázorněno v následující jazyka C# fragment kódu, která načte entity **PartitionKey** hodnoty "Prodeje" a  **RowKey** hodnoty "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak očekává, že v tomto příkladu entity načte typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Načítání více entit pomocí jazyka LINQ
Můžete načíst pomocí LINQ s klientskou knihovnu pro úložiště a zadat dotaz s více entit **kde** klauzuli. Chcete-li zabránit, prohledávání tabulky, by měla vždycky obsahovat **PartitionKey** hodnotu v poli kde klauzule a pokud je to možné **RowKey** hodnotu, aby prohledávání tabulky a oddílu. Služba table service podporuje omezenou sadu operátory porovnání (větší než, větší než nebo rovná, méně než, menší než nebo rovno, stejné a není rovno) pro použití v where – klauzule. Následující fragment kódu jazyka C# najde všechny zaměstnance, jejichž poslední jméno začíná písmenem "B" (za předpokladu, že **RowKey** ukládá příjmení) v prodejní oddělení (za předpokladu, že **PartitionKey** ukládá název oddělení):  

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

Následující příklad kódu ukazuje ekvivalentní funkce s použitím rozhraní fluent API (Další informace o rozhraní fluent API obecné naleznete v tématu [osvědčené postupy pro navrhování Fluent API](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Ukázka vnoří více **CombineFilters** metody mají být zahrnuty tři podmínky filtru.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Načítání velkého počtu entit z dotazu
Optimální dotaz vrací jednotlivé entity na základě **PartitionKey** hodnotu a **RowKey** hodnotu. V některých případech ale může mít požadavek vrátit entity ze stejného oddílu nebo dokonce z mnoha oddílů.  

V takových situacích by měl vždy plně testování výkonu vaší aplikace.  

Dotaz vůči službě table service může vrátit maximálně 1 000 entit najednou a mohou spouštět maximálně pět sekund. Pokud sada výsledků obsahuje víc než 1 000 entity, pokud dotaz nebyla dokončena do pěti sekund, nebo pokud dotaz překročí hranice oddílu, vrátí služba Table service token pro pokračování umožňuje klientské aplikaci požádat o další sadu entit. Další informace o jak pokračování tokeny práce, naleznete v tématu [časový limit dotazu a stránkování](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte klientskou knihovnu pro úložiště, je pokračování tokeny automaticky zpracovat za vás jako vrátí entity ze služby Table service. Následující vzorový kód C# pomocí klientskou knihovnu pro úložiště automaticky zpracovává pokračování tokeny služby table service je vrátí v odpovědi:  

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

Následující kód jazyka C# explicitně zpracovává pokračování tokeny:  

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

Pomocí explicitně pokračování tokenů, můžete řídit, když aplikace načte další segment data. Například pokud vaše klientská aplikace umožňuje uživatelům stránkovat entity, které jsou uložené v tabulce, uživatel se může rozhodnout přes všechny entity načíst pomocí dotazu tak, že vaše aplikace využije pouze token pro pokračování k načtení další stránky při segmentu uživatel měl dokončil stránkování prostřednictvím všechny entity v aktuálním segmentu. Tento přístup má několik výhod:  

* Je možné omezit objem dat pro načtení ze služby Table service umožňuje a přesunete přes síť.  
* To umožňuje provádět asynchronní vstupně-výstupní operace v rozhraní .NET.  
* Umožňuje vám k serializaci token pro pokračování do trvalého úložiště, takže můžete pokračovat v případě při selhání aplikace.  

> [!NOTE]
> Token pro pokračování obvykle vrátí segment obsahující 1 000 entity, i když může být méně. To platí také v případě, že omezíte počet položek, které dotaz vrátí pomocí **trvat** vrátit prvních n entity, které odpovídají vašim kritériím vyhledávání: služby table service může vrátit segment obsahující méně než n entity spolu s token pro pokračování umožňuje načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak upravit počtu entit vrácených v segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít nastavenou vlastnost až 255 a mít velikost až 1 MB. Při dotazování tabulky a načtení entit, nemusí potřebovat všechny vlastnosti a přenosu dat zbytečně (Chcete-li snížit latenci a náklady na) se můžete vyhnout. Projekce na straně serveru můžete použít pro přenos pouze vlastnosti, které potřebujete. V následujícím příkladu se načte jenom **e-mailu** vlastnosti (spolu s **PartitionKey**, **RowKey**, **časové razítko**a **ETag**) z entity vybrané v dotazu.  

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

Všimněte si, že jak **RowKey** hodnota není k dispozici, i když nebyla zahrnuta v seznamu vlastností, které mají načíst.  

## <a name="modifying-entities"></a>Úprava entit
Klientská knihovna pro úložiště umožňuje upravit entity ve službě table service ukládaná vkládání, odstraňování a aktualizaci entity. Vám pomůže EGTs dávkové více insert, update a operace odstranění společně a snížit počet zpátečních cest vyžaduje a zvýšit výkon vašeho řešení.  

Chtěli bychom upozornit, že výjimky vyvolána, když klientská knihovna pro úložiště provede EGT obvykle zahrnují index entity, která způsobila dávky, která selžou. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh má vliv na způsob, jakým klientské aplikace zpracovává operace souběžnosti a aktualizace.  

### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení, implementuje optimistického řízení souběžnosti kontroly na úrovni jednotlivých entit pro služby table service **vložit**, **sloučit**, a **odstranit** operace, i když ho je možné pro klienta služby table service obejít tyto kontroly vynutí. Další informace o tom, jak služby table service spravují souběžnost najdete v tématu [Správa souběžnosti v Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Sloučení nebo nahradit
**Nahradit** metodu **TableOperation** třídy vždy nahradí kompletní entity ve službě Table service. Pokud neuvedete vlastnost v požadavku, tuto vlastnost existuje v uložených entity, požadavek Odebere tuto vlastnost z uložené entity. Pokud chcete explicitně odebrání vlastnosti z uložené entity, je nutné zahrnout každou vlastnost v požadavku.  

Můžete použít **sloučit** metodu **TableOperation** třídy ke snížení množství dat, která odesíláte do služby Table service, pokud chcete aktualizovat entitu. **Sloučit** metoda nahradí všechny vlastnosti v entitě uložené hodnoty vlastností z entity zahrnutý v požadavku, ale ponechá beze změn, všechny vlastnosti v uložených entity, které nejsou zahrnuté v požadavku. To je užitečné, pokud máte velké entity a stačí aktualizovat malý počet vlastností v požadavku.  

> [!NOTE]
> **Nahradit** a **sloučit** metody selhat, pokud entita neexistuje. Jako alternativu můžete použít **InsertOrReplace** a **InsertOrMerge** metody, které vytvářejí nové entity, pokud neexistuje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Práce s typy heterogenní entit
Služba Table service je *bez schématu* tabulky úložiště, to znamená, že jedné tabulky můžete ukládat entity více typů poskytuje skvělou flexibilitu při návrhu. Následující příklad ukazuje tabulku ukládání zaměstnanci a oddělení entity:  

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
<th>LastName</th>
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
<th>LastName</th>
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
<th>LastName</th>
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

Všimněte si, že každá entita musí mít stále **PartitionKey**, **RowKey**, a **časové razítko** hodnoty, ale mohou mít libovolnou sadu vlastností. Kromě toho není nutné nic označují typ entity, pokud se nepřihlásíte k ukládání těchto informací někde. Existují dvě možnosti pro určení typu entity:  

* Předřaďte typ entity, který má **RowKey** (nebo případně **PartitionKey**). Například **EMPLOYEE_000123** nebo **DEPARTMENT_SALES** jako **RowKey** hodnoty.  
* Samostatné vlastnost slouží k zaznamenání typ entity, jak je znázorněno v následující tabulce.  

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
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnance</td>
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
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnance</td>
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
<th>LastName</th>
<th>Věk</th>
<th>Email</th>
</tr>
<tr>
<td>Zaměstnance</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Typ první možnost předřazení subjektem, který **RowKey**, je užitečné, pokud je možné, že dvě entity, které různých typů může mít stejnou hodnotu klíče. Také skupin entit stejného typu společně v oddílu.  

Techniky popsané v této části jsou obzvláště důležité pro diskuse [vztahy dědičnosti](table-storage-design-modeling.md#inheritance-relationships) výše v tomto průvodci v článku [modelovat vztahy](table-storage-design-modeling.md).  

> [!NOTE]
> Měli byste zvážit, včetně číslo verze v hodnotě entity typu umožňují klientským aplikacím se vyvíjet objektů POCO a pracovat s různými verzemi aplikací.  
> 
> 

Zbývající část Tato část popisuje některé funkce v klientské knihovně pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

### <a name="retrieving-heterogeneous-entity-types"></a>Načítání typů heterogenní entity
Pokud používáte klientskou knihovnu pro úložiště, máte tři možnosti pro práci s více typy entit.  

Pokud je, že typ entity uložená s konkrétním **RowKey** a **PartitionKey** hodnoty, pokud načítáte entity, jak je znázorněno v předchozích dvou příkladech můžete zadat typ entity, která načtení entit typu **EmployeeEntity**: [provádění dotazu bodu pomocí klientskou knihovnu pro úložiště](#executing-a-point-query-using-the-storage-client-library) a [načítání více entit pomocí jazyka LINQ](#retrieving-multiple-entities-using-linq).  

Druhou možností je použít **DynamicTableEntity** typ (kontejner objektů) místo konkrétní typ entity POCO (Tato možnost může také zvýšit výkon, protože není nutné k serializaci a deserializaci entita, která má typy rozhraní .NET). Následující kód jazyka C# potenciálně načte více entit různých typů z tabulky, ale vrací všechny entity jako **DynamicTableEntity** instancí. Poté použije **EntityType** a určí typ jednotlivých entit:  

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

Všimněte si, že k načtení dalších vlastností je třeba použít **TryGetValue** metodu **vlastnosti** vlastnost **DynamicTableEntity** třídy.  

Třetí možností je Kombinujte pomocí **DynamicTableEntity** typ a **EntityResolver** instance. To umožňuje řešení pro více typů POCO ve stejném dotazu. V tomto příkladu **EntityResolver** delegát používá **EntityType** vlastnost k rozlišení mezi těmito dvěma typy entit, které dotaz vrátí. **Vyřešit** metoda používá **překladač** delegáta k vyřešení **DynamicTableEntity** instance na **TableEntity** instancí.  

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
Není potřeba znát typ entity ho odstranit a budete vždycky vědět typ entity, při vložení. Můžete však použít **DynamicTableEntity** typ pro aktualizaci entity bez znalosti jeho typ a nemusíte psát třídu entity objektů POCO. Následující vzorový kód načte jednu entitu a zkontroluje, **EmployeeCount** existuje vlastnost před její aktualizací.  

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

## <a name="controlling-access-with-shared-access-signatures"></a>Řízení přístupu se sdílenými přístupovými podpisy
Tokeny sdíleného přístupového podpisu (SAS) můžete povolit klientské aplikace upravit (a dotazování) tabulkové entity, aniž by bylo nutné zahrnout klíč účtu úložiště ve vašem kódu. Obvykle jsou k dispozici tři hlavní výhody použití SAS ve vaší aplikaci:  

* Nepotřebujete distribuovat klíč účtu úložiště pro nezabezpečené platformy (jako jsou mobilní zařízení), aby toto zařízení pro přístup a úpravy entit ve službě Table service.  
* Snižování zátěže určitou část práce, která webové a pracovní role provádět správu vaší entity na klientských zařízeních, jako je například počítačích koncových uživatelů a mobilních zařízení.  
* Můžete přiřadit omezeného a čas omezenou sadu oprávnění pro klienta (například možnost povolit přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o použití tokenů SAS pomocí služby Table service, najdete v části [použití sdílených přístupových podpisů (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Však musí i nadále generovat tokeny SAS, které udělují klientské aplikace do entity ve službě table service: byste měli udělat v prostředí, které se má zabezpečit přístup do své klíče účtu úložiště. Obvykle použijete webové nebo pracovní role generovat tokeny SAS a doručujte je na klientských aplikací, které potřebují přístup k vaší entity. Vzhledem k tomu, že se stále vyžaduje režii účastnící se vytváření a doručování tokeny SAS pro klienty, zvažte, jak nejlépe omezit tato režie, zejména v velkoobjemových scénářů.  

Je možné vytvořit token SAS, která uděluje přístup k podmnožině entit v tabulce. Ve výchozím nastavení, můžete vytvořit token SAS pro celou tabulku, ale je také možné zadat, že SAS token udělit přístup k buď celou řadu **PartitionKey** hodnoty nebo celou řadu **PartitionKey** a **RowKey** hodnoty. Můžete zvolit ke generování tokenů SAS pro jednotlivé uživatele systému tak, aby každý uživatel tokenu SAS pouze jim umožňuje přístup k vlastní entity ve službě table service.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Pokud jsou rozprostírá vaše požadavky napříč několika oddíly, propustnosti a klienta reakce můžete zlepšit pomocí asynchronní a paralelní dotazy.
Například může mít dvě nebo víc instancí rolí pracovního procesu přístup k vaší tabulky paralelně. Může mít jednotlivé pracovní role za konkrétní sady oddílů nebo jednoduše mít více instancí rolí pracovního procesu, každá mít přístup všechny oddíly v tabulce.  

V rámci instance klienta můžete zlepšit propustnost spuštěním storage operace asynchronně. Klientská knihovna pro úložiště usnadňuje zápis asynchronní dotazy a úpravy. Můžete například začít se synchronní metoda, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu C#:  

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

Můžete snadno upravit tento kód tak, aby spouští dotaz asynchronně následujícím způsobem:  

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

V tomto příkladu asynchronní uvidíte následující změny z synchronní verze:  

* Podpis metody zahrnuje nyní **asynchronní** modifikátor a vrátí **úloh** instance.  
* Namísto volání metody **ExecuteSegmented** volá metody k získání výsledků, metoda nyní **ExecuteSegmentedAsync** metody a použije **await** modifikátor načtěte výsledky asynchronně.  

Klientská aplikace může tuto metodu volat více než jednou (s různými hodnotami parametru **oddělení** parametr), a každý dotaz se spustí na samostatném vlákně.  

Mějte na paměti, že neexistuje žádná asynchronní verze **Execute** metodu **TableQuery** třídy, protože **IEnumerable** rozhraní nepodporuje asynchronní výčet.  

Můžete také vložit, aktualizovat a odstraňovat entity asynchronně. Následující příklad jazyka C# ukazuje jednoduchý, která je synchronní metoda vložení nebo nahrazení entity zaměstnance:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Můžete snadno upravit tento kód tak, aby aktualizace běží asynchronně následujícím způsobem:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto příkladu asynchronní uvidíte následující změny z synchronní verze:  

* Podpis metody zahrnuje nyní **asynchronní** modifikátor a vrátí **úloh** instance.  
* Namísto volání metody **Execute** metoda aktualizovat entitu, metoda teď volá **ExecuteAsync** metody a použije **await** modifikátor k načtení výsledků asynchronně.  

Klientská aplikace může volat více asynchronních metod, jako je ten, a každé volání metody se spustí na samostatném vlákně.  

## <a name="next-steps"></a>Další postup

- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)