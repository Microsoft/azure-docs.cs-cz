---
title: Vzory návrhu tabulky úložišť Azure | Dokumenty společnosti Microsoft
description: Použijte vzory pro řešení azure table service.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529335"
---
# <a name="table-design-patterns"></a>Způsoby návrhu tabulek
Tento článek popisuje některé vzory vhodné pro použití s řešení služby Table Service. Také uvidíte, jak můžete prakticky řešit některé problémy a kompromisy popsané v jiných článcích návrhu úložiště tabulky. Následující diagram shrnuje vztahy mezi různými vzory:  

![vyhledat související data](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mapa vzorů výše zvýrazní některé vztahy mezi vzory (modrá) a anti-patterns (oranžová), které jsou popsány v této příručce. Existuje mnoho dalších vzorů, které stojí za zvážení. Jedním z klíčových scénářů pro službu Table Service je například použití [vzoru materializovaného zobrazení](https://msdn.microsoft.com/library/azure/dn589782.aspx) ze vzoru [CQRS (Command Query Responsibility Segregation).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Vzor sekundárního indexu uvnitř oddílu
Uložte více kopií každé entity pomocí různých hodnot **RowKey** (ve stejném oddílu), abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.** Aktualizace mezi kopiemi mohou být konzistentní pomocí EGTs.  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey.** To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí níže uvedené struktury tabulky může klientská aplikace použít bodový dotaz k načtení jednotlivé entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty **PartitionKey** a **RowKey).** Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.

![Obrázek06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Pokud také chcete mít možnost najít entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, musíte použít méně efektivní prohledávač oddílů k nalezení shody. Důvodem je, že služba table service neposkytuje sekundární indexy. Kromě toho neexistuje žádná možnost požádat o seznam zaměstnanců seřazených v jiném pořadí než **RowKey** pořadí.  

### <a name="solution"></a>Řešení
Chcete-li obejít nedostatek sekundárních indexů, můžete uložit více kopií každé entity s každou kopií pomocí jiné hodnoty **RowKey.** Pokud ukládáte entitu s níže uvedenými strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro **řádek klíč**, "empid_" a "email_" umožňují dotaz na jednoho zaměstnance nebo rozsah zaměstnanců pomocí rozsahu e-mailové adresy nebo ID zaměstnance.  

![Entity zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Následující dvě kritéria filtru (jedno vyhledání podle ID zaměstnance a jedno vyhledání podle e-mailové adresy) určují bodové dotazy:  

* $filter=(PartitionKey eq 'Prodej') a (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Prodej') a (RowKey eq 'email_jonesj@contoso.com')  

Pokud se dotazujete na řadu entit zaměstnanců, můžete zadat rozsah seřazený v pořadí ID zaměstnance nebo rozsah seřazený v pořadí e-mailových adres dotazováním na entity s příslušnou předponou v **řádku .**  

* Chcete-li najít všechny zaměstnance v oddělení prodeje s ID zaměstnance v rozsahu 000100 až 000199 použití: $filter=(PartitionKey eq 'Prodej') a (RowKey ge 'empid_000100') a (RowKey le 'empid_000199')  
* Chcete-li najít všechny zaměstnance v oddělení prodeje s e-mailovou adresou začínající písmenem 'a' use: $filter=(PartitionKey eq 'Sales') a (RowKey ge 'email_a') a (RowKey lt 'email_b')  
  
  Syntaxe filtru použitá ve výše uvedených příkladech je z rozhraní REST API služby Table Service, další informace naleznete v [tématu Entity dotazu](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Úložiště tabulky je relativně levné použití, takže náklady na ukládání duplicitních dat by neměly být velkým problémem. Vždy byste však měli vyhodnotit náklady na návrh na základě očekávaných požadavků na úložiště a přidat pouze duplicitní entity pro podporu dotazů, které bude klientská aplikace spouštět.  
* Vzhledem k tomu, že sekundární index entity jsou uloženy ve stejném oddílu jako původní entity, měli byste zajistit, že nepřekročíte cíle škálovatelnosti pro jednotlivé oddíly.  
* Duplicitní entity můžete udržovat konzistentní mezi sebou pomocí EGTs aktualizovat dvě kopie entity atomicky. To znamená, že byste měli uložit všechny kopie entity ve stejném oddílu. Další informace naleznete v části [Použití transakcí skupiny entit](table-storage-design.md#entity-group-transactions).  
* Hodnota použitá pro **Řádek Musí** být jedinečný pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Odsazení číselné hodnoty v **RowKey** (například ID zaměstnance 000223), umožňuje správné řazení a filtrování na základě horní a dolní hranice.  
* Není nutné duplikovat všechny vlastnosti entity. Například pokud dotazy, které vyhledává entity pomocí e-mailové adresy v **RowKey** nikdy potřebovat věk zaměstnance, tyto entity může mít následující strukturu:

   ![Struktura entity zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Obvykle je lepší ukládat duplicitní data a ujistěte se, že můžete načíst všechna data, která potřebujete s jedním dotazem, než použít jeden dotaz k vyhledání entity a jiný vyhledat požadovaná data.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte v případě, že klientská aplikace potřebuje načíst entity pomocí různých klíčů, když váš klient potřebuje načíst entity v různých pořadí řazení a kde můžete identifikovat každou entitu pomocí různých jedinečných hodnot. Měli byste si však být jisti, že nepřekročíte limity škálovatelnosti oddílu při vyhledávání entit pomocí různých hodnot **RowKey.**  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern)
* [Vzor složeného klíče](#compound-key-pattern)
* Transakce skupiny entit
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Vzor sekundárního indexu mezi oddíly
Uložte více kopií každé entity pomocí různých hodnot **RowKey** v samostatných oddílech nebo v samostatných tabulkách, abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.**  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey.** To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí níže uvedené struktury tabulky může klientská aplikace použít bodový dotaz k načtení jednotlivé entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty **PartitionKey** a **RowKey).** Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.  

![Identifikační číslo zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Pokud také chcete mít možnost najít entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, musíte použít méně efektivní prohledávač oddílů k nalezení shody. Důvodem je, že služba table service neposkytuje sekundární indexy. Kromě toho neexistuje žádná možnost požádat o seznam zaměstnanců seřazených v jiném pořadí než **RowKey** pořadí.  

Očekáváte vysoký objem transakcí proti těmto entitám a chcete minimalizovat riziko omezení služby Table service vašeho klienta.  

### <a name="solution"></a>Řešení
Chcete-li obejít nedostatek sekundárníindexy, můžete uložit více kopií každé entity s každou kopii pomocí různých **PartitionKey** a **RowKey** hodnoty. Pokud ukládáte entitu s níže uvedenými strukturami, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro **partitionkey**, "empid_" a "email_" umožňují určit, který index chcete použít pro dotaz.  

![Primární index a sekundární index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Následující dvě kritéria filtru (jedno vyhledání podle ID zaměstnance a jedno vyhledání podle e-mailové adresy) určují bodové dotazy:  

* $filter=(PartitionKey eq 'empid_Sales') a (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') a (RowKey eq 'jonesj@contoso.com')  

Pokud se dotazujete na řadu entit zaměstnanců, můžete zadat rozsah seřazený v pořadí ID zaměstnance nebo rozsah seřazený v pořadí e-mailových adres dotazováním na entity s příslušnou předponou v **řádku .**  

* Chcete-li najít všechny zaměstnance v oddělení prodeje s ID zaměstnance v rozsahu **000100** až **000199** seřazené v použití id zaměstnance: $filter=(PartitionKey eq 'empid_Sales') a (RowKey ge '000100') a (RowKey le '000199')  
* Chcete-li najít všechny zaměstnance v oddělení prodeje s e-mailovou adresou, která začíná na 'a' seřazené v pořadí e-mailové adresy: $filter =(PartitionKey eq 'email_Sales') a (RowKey ge 'a') a (RowKey lt 'b')  

Syntaxe filtru použitá ve výše uvedených příkladech je z rozhraní REST API služby Table Service, další informace naleznete v [tématu Entity dotazu](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Duplicitní entity můžete zachovat v konečném souladu s použitím [nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern) udržovat primární a sekundární index entity.  
* Úložiště tabulky je relativně levné použití, takže náklady na ukládání duplicitních dat by neměly být velkým problémem. Vždy byste však měli vyhodnotit náklady na návrh na základě očekávaných požadavků na úložiště a přidat pouze duplicitní entity pro podporu dotazů, které bude klientská aplikace spouštět.  
* Hodnota použitá pro **Řádek Musí** být jedinečný pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Odsazení číselné hodnoty v **RowKey** (například ID zaměstnance 000223), umožňuje správné řazení a filtrování na základě horní a dolní hranice.  
* Není nutné duplikovat všechny vlastnosti entity. Například pokud dotazy, které vyhledává entity pomocí e-mailové adresy v **RowKey** nikdy potřebovat věk zaměstnance, tyto entity může mít následující strukturu:
  
   ![Zaměstnanecká entita (sekundární index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Obvykle je lepší ukládat duplicitní data a ujistěte se, že můžete načíst všechna data, která potřebujete s jedním dotazem, než použít jeden dotaz k vyhledání entity pomocí sekundárního indexu a další vyhledat požadovaná data v primárním indexu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte v případě, že klientská aplikace potřebuje načíst entity pomocí různých klíčů, když váš klient potřebuje načíst entity v různých pořadí řazení a kde můžete identifikovat každou entitu pomocí různých jedinečných hodnot. Tento vzor použijte, pokud chcete vyhnout překročení limitů škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých hodnot **RowKey.**  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor sekundárního indexu uvnitř oddílu](#intra-partition-secondary-index-pattern)  
* [Vzor složeného klíče](#compound-key-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Nakonec konzistentní transakce vzor
Povolte nakonec konzistentní chování přes hranice oddílu nebo hranice systému úložiště pomocí front Azure.  

### <a name="context-and-problem"></a>Kontext a problém
EGTs povolit atomické transakce napříč více entit, které sdílejí stejný klíč oddílu. Z důvodů výkonu a škálovatelnosti se můžete rozhodnout ukládat entity, které mají požadavky na konzistenci v samostatných oddílech nebo v samostatném systému úložiště: v takovém scénáři nelze použít EGTs k udržení konzistence. Můžete mít například požadavek zachovat konečnou konzistenci mezi:  

* Entity uložené ve dvou různých oddílech ve stejné tabulce, v různých tabulkách nebo v různých účtech úložiště.  
* Entita uložená ve službě Table a objekt blob uložený ve službě Objektů blob.  
* Entita uložená ve službě Table service a soubor v systému souborů.  
* Entita uložená ve službě Table, ale indexovaná pomocí služby Azure Cognitive Search.  

### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které poskytuje konečnou konzistenci mezi dvěma nebo více oddíly nebo systémy úložiště.
Pro ilustraci tohoto přístupu předpokládejme, že máte požadavek, abyste mohli archivovat staré entity zaměstnanců. Staré entity zaměstnanců jsou zřídka dotazovány a měly by být vyloučeny z aktivit, které se zabývají aktuálními zaměstnanci. Chcete-li tento požadavek implementovat, uložíte aktivní zaměstnance do tabulky **Aktuální** a staří zaměstnanci v tabulce **Archiv.** Archivace zaměstnance vyžaduje odstranění entity z **aktuální** tabulky a přidání entity do tabulky **Archiv,** ale k provedení těchto dvou operací nelze použít EGT. Aby se zabránilo riziku, že selhání způsobí, že se entita zobrazí v obou nebo ani v jedné tabulkách, musí být operace archivace nakonec konzistentní. Následující sekvenční diagram popisuje kroky v této operaci. Další podrobnosti jsou uvedeny pro cesty výjimek v následujícím textu.  

![Řešení front Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient zahájí operaci archivace umístěním zprávy do fronty Azure, v tomto příkladu archivovat zaměstnance #456. Role pracovního procesu dotazování fronty pro nové zprávy; když najde jeden, přečte zprávu a ponechá skrytou kopii ve frontě. Role pracovníka dále načte kopii entity z **aktuální** tabulky, vloží kopii do tabulky **Archiv** a potom odstraní originál z **aktuální** tabulky. Nakonec pokud nebyly žádné chyby z předchozích kroků, role pracovního procesu odstraní skrytou zprávu z fronty.  

V tomto příkladu krok 4 vloží zaměstnance do tabulky **Archiv.** Může přidat zaměstnance do objektu blob ve službě Blob nebo souboru v systému souborů.  

### <a name="recovering-from-failures"></a>Zotavuje se z poruch
Je důležité, aby operace v krocích **4** a **5** musí být *idempotentní* v případě, že role pracovníka potřebuje restartovat operaci archivace. Pokud používáte službu Tabulka, pro krok **4** byste měli použít operaci "vložit nebo nahradit"; Pro krok **5** byste měli použít operaci "odstranit, pokud existuje" v klientské knihovně, kterou používáte. Pokud používáte jiný úložný systém, musíte použít příslušnou operaci idempotent.  

Pokud role pracovního procesu nikdy nedokončí krok **6**, pak po časovém výpadku se zpráva znovu zobrazí ve frontě připravená pro roli pracovního procesu, aby se ji pokusila znovu zpracovat. Role pracovního procesu můžete zkontrolovat, kolikrát byla přečtena zpráva ve frontě a v případě potřeby označit, že se jedná o "poškozenou" zprávu pro šetření odesláním do samostatné fronty. Další informace o čtení zpráv fronty a kontrole počtu vyřazení z fronty naleznete v tématu [Získání zpráv](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Některé chyby ze služby Tabulka a Fronty jsou přechodné chyby a klientská aplikace by měla obsahovat vhodnou logiku opakování pro jejich zpracování.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje izolaci transakcí. Klient může například číst **aktuální** a **archivní** tabulky, když role pracovního procesu byla mezi kroky **4** a **5**a zobrazit nekonzistentní zobrazení dat. Data budou nakonec konzistentní.  
* Musíte si být jisti, že kroky 4 a 5 jsou idempotentní, aby byla zajištěna konečná konzistence.  
* Řešení můžete škálovat pomocí více front a instancí role pracovního procesu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud chcete zaručit konečnou konzistenci mezi entitami, které existují v různých oddílech nebo tabulkách. Tento vzor můžete rozšířit, abyste zajistili konečnou konzistenci pro operace napříč službou Table service a službou Blob a dalšími zdroji dat než Azure Storage, jako je databáze nebo systém souborů.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit  
* [Sloučit nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud izolace transakcí je důležité pro vaše řešení, měli byste zvážit přepracování tabulky, které vám umožní používat EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Vzor entity indexu
Udržovat entity indexu povolit efektivní vyhledávání, které vracejí seznamy entit.  

### <a name="context-and-problem"></a>Kontext a problém
Služba Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey.** To umožňuje klientské aplikaci efektivně načíst entitu pomocí bodového dotazu. Například pomocí níže uvedené struktury tabulky může klientská aplikace efektivně načíst jednotlivé entity zaměstnance pomocí názvu oddělení a ID zaměstnance **(PartitionKey** a **RowKey).**  

![Zaměstnanecká entita](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Pokud také chcete mít možnost načíst seznam entit zaměstnanců na základě hodnoty jiné nejedinečné vlastnosti, jako je například jejich příjmení, je nutné použít méně efektivní prohledávací oddíl k nalezení shody, nikoli pomocí indexu k jejich přímému vyhledávání. Důvodem je, že služba table service neposkytuje sekundární indexy.  

### <a name="solution"></a>Řešení
Chcete-li povolit vyhledávání podle příjmení s výše uvedenou strukturou entity, je nutné udržovat seznamy ID zaměstnanců. Pokud chcete načíst entity zaměstnance s určitým příjmením, například Jones, musíte nejprve vyhledat seznam ID zaměstnanců pro zaměstnance s Jonesem jako jejich příjmením a potom tyto entity zaměstnance načíst. Existují tři hlavní možnosti pro uložení seznamů ID zaměstnanců:  

* Použijte úložiště objektů blob.  
* Vytvořte entity indexu ve stejném oddílu jako entity zaměstnanců.  
* Vytvořte entity indexu v samostatném oddílu nebo tabulce.  

<u>Možnost #1: Použití úložiště objektů blob</u>  

Pro první možnost vytvoříte objekt blob pro každé jedinečné příjmení a v každém úložišti objektů blob seznam hodnot **PartitionKey** (oddělení) a **RowKey** (ID zaměstnance) pro zaměstnance, kteří mají toto příjmení. Když přidáte nebo odstraníte zaměstnance, měli byste zajistit, aby byl obsah příslušného objektu blob nakonec konzistentní s entitami zaměstnance.  

<u>Možnost #2:</u> Vytvoření entit indexu ve stejném oddílu  

Pro druhou možnost použijte entity indexu, které ukládají následující data:  

![Entita indexu zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Vlastnost **ID zaměstnanců** obsahuje seznam ID zaměstnanců s příjmením uloženým v **řádku**.  

Následující kroky popisují proces, který byste měli dodržovat při přidávání nového zaměstnance, pokud používáte druhou možnost. V tomto příkladu přidáváme zaměstnance s ID 000152 a příjmením Jones v oddělení prodeje:  

1. Načíst entitu indexu s hodnotou **PartitionKey** "Prodej" a **RowKey** hodnota "Jones." Uložte eTag této entity pro použití v kroku 2.  
2. Vytvořte transakci skupiny entit (tj. dávkovou operaci), která vloží novou entitu zaměstnance (**Hodnota PartitionKey** "Prodej" a Hodnota **řádku** "000152") a aktualizuje entitu indexu (**Hodnota PartitionKey** "Prodej" a **Hodnota Řádku** "Jones") přidáním nového ID zaměstnance do seznamu v poli EmployeeIDs. Další informace o transakcích skupiny entit naleznete v tématu Transakce skupiny entit.  
3. Pokud transakce skupiny entit selže z důvodu optimistické chyby souběžnosti (entita indexu právě upravil někdo jiný), musíte začít znovu v kroku 1 znovu.  

Podobný přístup můžete použít k odstranění zaměstnance, pokud používáte druhou možnost. Změna příjmení zaměstnance je o něco složitější, protože budete muset provést transakci skupiny entit, která aktualizuje tři entity: entitu zaměstnance, entitu indexu pro staré příjmení a entitu indexu pro nové příjmení. Je nutné načíst každou entitu před provedením jakékoli změny, aby bylo možné načíst hodnoty ETag, které pak můžete použít k provedení aktualizací pomocí optimistické souběžnosti.  

Následující kroky popisují proces, který byste měli dodržovat, když potřebujete vyhledat všechny zaměstnance s daným příjmením v oddělení, pokud používáte druhou možnost. V tomto příkladu hledáme všechny zaměstnance s příjmením Jones v oddělení prodeje:  

1. Načíst entitu indexu s hodnotou **PartitionKey** "Prodej" a **RowKey** hodnota "Jones."  
2. Analyzovat seznam ID zaměstnanců v poli ID zaměstnanců.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanců (například jejich e-mailové adresy), načtěte každou entitu zaměstnance pomocí hodnoty **PartitionKey** "Prodej" a **RowKey** ze seznamu zaměstnanců, které jste získali v kroku 2.  

<u>Možnost #3:</u> Vytvoření entit indexu v samostatném oddílu nebo tabulce  

Pro třetí možnost použijte entity indexu, které ukládají následující data:  

![Entita indexu zaměstnance v samostatném oddílu](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Vlastnost **ID zaměstnanců** obsahuje seznam ID zaměstnanců s příjmením uloženým v **řádku**.  

S třetí možnost nelze použít EGTs k udržení konzistence, protože entity indexu jsou v samostatném oddílu od entit zaměstnanců. Ujistěte se, že entity indexu jsou nakonec konzistentní s entitami zaměstnanců.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje alespoň dva dotazy k načtení odpovídající entity: jeden dotaz entity indexu získat seznam **RowKey** hodnoty a pak dotazy na načíst každou entitu v seznamu.  
* Vzhledem k tomu, že jednotlivá entita má maximální velikost 1 MB, možnost #2 a možnost #3 v řešení předpokládají, že seznam ID zaměstnance pro dané příjmení není nikdy větší než 1 MB. Pokud je seznam ID zaměstnanců pravděpodobně větší než 1 MB, použijte možnost #1 a uložte data indexu do úložiště objektů blob.  
* Pokud použijete možnost #2 (pomocí EGTs pro zpracování přidání a odstranění zaměstnanců a změnu příjmení zaměstnance), musíte vyhodnotit, pokud objem transakcí se blíží omezení škálovatelnosti v daném oddílu. Pokud se jedná o tento případ, měli byste zvážit nakonec konzistentní řešení (možnost #1 nebo možnost #3), který používá fronty pro zpracování požadavků na aktualizaci a umožňuje ukládat entity indexu v samostatném oddílu od entit zaměstnanců.  
* Možnost #2 v tomto řešení předpokládá, že chcete vyhledat podle příjmení v rámci oddělení: například chcete načíst seznam zaměstnanců s příjmením Jones v oddělení prodeje. Pokud chcete mít možnost vyhledat všechny zaměstnance s příjmením Jones v celé organizaci, použijte možnost #1 nebo možnost #3.
* Můžete implementovat řešení založené na frontě, které poskytuje konečnou konzistenci (viz [nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern) pro další podrobnosti).  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud chcete vyhledat sadu entit, které všechny sdílejí hodnotu společné vlastnosti, například všechny zaměstnance s příjmením Jones.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Vzor denormalizace
Zkombinujte související data do jedné entity a umožníte vám načíst všechna data, která potřebujete, pomocí dotazu s jedním bodem.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle normalizujete data, abyste odstranili duplikaci, což vede k dotazům, které načítají data z více tabulek. Pokud normalizujete data v tabulkách Azure, musíte provést více zpátečních cest z klienta na server, abyste načetli související data. Například s níže uvedenou strukturou tabulky potřebujete dvě zpáteční cesty k načtení podrobností pro oddělení: jeden pro načtení entity oddělení, která obsahuje ID manažera, a pak další požadavek na načtení podrobností manažera v entitě zaměstnance.  

![Entita oddělení a zaměstnanec](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Řešení
Namísto ukládání dat ve dvou samostatných entitách denormalizovat data a uchovávat kopii podrobnosti vedoucího v entitě oddělení. Například:  

![Subjekt oddělení](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

S entitami oddělení uloženými s těmito vlastnostmi můžete nyní načíst všechny podrobnosti, které potřebujete o oddělení pomocí bodového dotazu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* K dispozici je některé náklady režie spojené s ukládáním některých dat dvakrát. Výhoda výkonu (vyplývající z menšího počtu požadavků na službu úložiště) obvykle převažuje nad marginálním zvýšením nákladů na úložiště (a tyto náklady jsou částečně kompenzovány snížením počtu transakcí, které potřebujete k načtení podrobností oddělení ).  
* Je nutné zachovat konzistenci dvou entit, které ukládají informace o manažerech. Problém konzistence můžete zpracovat pomocí EGTs aktualizovat více entit v jedné atomické transakce: v tomto případě entity oddělení a entita zaměstnance pro vedoucíoddělení jsou uloženy ve stejném oddílu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud často potřebujete vyhledat související informace. Tento vzor snižuje počet dotazů, které musí klient provést, aby načetl data, která vyžaduje.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Vzor složeného klíče
Pomocí složených hodnot **RowKey** umožníte klientovi vyhledávat související data pomocí dotazu s jedním bodem.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je přirozené použít spojení v dotazech k vrácení souvisejících částí dat klientovi v jednom dotazu. Můžete například použít ID zaměstnance k vyhledání seznamu souvisejících entit, které obsahují údaje o výkonu a kontrole pro tohoto zaměstnance.  

Předpokládejme, že ukládáte entity zaměstnanců ve službě Table pomocí následující struktury:  

![Struktura entity zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Musíte také ukládat historická data týkající se recenzí a výkonu za každý rok, kdy zaměstnanec pracoval pro vaši organizaci, a musíte mít přístup k těmto informacím podle roku. Jednou z možností je vytvoření jiné tabulky, ve které jsou uloženy entity s následující strukturou:  

![Alternativní struktura zaměstnaneckých entit](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Všimněte si, že s tímto přístupem se můžete rozhodnout duplikovat některé informace (například křestní jméno a příjmení) v nové entitě, abyste mohli načíst data s jedním požadavkem. Nelze však zachovat silnou konzistenci, protože nelze použít EGT k aktualizaci dvou entit atomicky.  

### <a name="solution"></a>Řešení
Nový typ entity můžete uložit do původní tabulky pomocí entit s následující strukturou:  

![Řešení pro strukturu zaměstnaneckých entit](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Všimněte **si,** jak RowKey je nyní složený klíč složený z ID zaměstnance a rok revize dat, která umožňuje načíst výkon zaměstnance a zkontrolovat data s jedním požadavkem pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data kontroly pro konkrétního zaměstnance (například zaměstnanec 000123 v prodejním oddělení):  

$filter=(PartitionKey eq 'Sales') a (RowKey ge 'empid_000123') a (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který usnadňuje analýzu **RowKey** hodnotu: například **000123_2012**.  
* Tuto entitu také ukládáte do stejného oddílu jako ostatní entity, které obsahují související data pro stejného zaměstnance, což znamená, že můžete použít EGTs k udržení silné konzistence.
* Měli byste zvážit, jak často budete dotaz ovat data k určení, zda je tento vzor vhodný.  Pokud například přistupujete k údajům o recenzích zřídka a k hlavním údajům o zaměstnancích často máte přístup jako samostatné entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete uložit jednu nebo více souvisejících entit, na které často zazníváte dotaz.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Vzorek ocasu protokolu
Načíst *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí v pořadí reverzní datum a čas.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je načíst naposledy vytvořené entity, například 10 posledních nároků na výdaje odeslaných zaměstnancem. Dotazy na tabulky podporují operaci **dotazu $top** vrátit první *n* entity ze sady: neexistuje žádná ekvivalentní operace dotazu vrátit poslední n entity v sadě.  

### <a name="solution"></a>Řešení
Uložte entity pomocí **RowKey,** který přirozeně seřadí v obráceném pořadí data a času pomocí tak, aby poslední položka je vždy první v tabulce.  

Chcete-li například načíst 10 nejnovějších nároků výdajů odeslaných zaměstnancem, můžete použít hodnotu storna značky odvozenou od aktuálního data a času. Následující ukázka kódu jazyka C# ukazuje jeden způsob, jak vytvořit vhodnou hodnotu "invertované značky" pro **Řádek,** který seřadí od nejnovějších po nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Můžete se vrátit k hodnotě data času pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Dotaz na tabulku vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Musíte pad reverzní tick hodnotu s úvodní nuly zajistit hodnotu řetězce seřadí podle očekávání.  
* Musíte si být vědomi cíle škálovatelnosti na úrovni oddílu. Dávejte pozor, abyste nevytvářeli aktivní bod oddíly.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete přístup k entitám v obráceném pořadí data a času nebo pokud potřebujete přístup k naposledy přidaným entitám.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Prepend / připojit anti-vzor](#prepend-append-anti-pattern)  
* [Načítání entit](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Vzorek odstranění s velkým objemem
Povolit odstranění velkého objemu entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce; odstraníte entity odstraněním tabulky.  

### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstraní stará data, která již nemusí být k dispozici pro klientskou aplikaci nebo že aplikace byla archivována na jiné paměťové médium. Tato data obvykle identifikujete podle data: například máte požadavek na odstranění záznamů všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jedním z možných návrhje použít datum a čas žádosti o přihlášení v **RowKey**:  

![Datum a čas pokusu o přihlášení](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Tento přístup zabraňuje oddíl hotspotů, protože aplikace můžete vložit a odstranit přihlašovací entity pro každého uživatele v samostatném oddílu. Tento přístup však může být nákladné a časově náročné, pokud máte velký počet entit, protože nejprve je třeba provést prohledávání tabulky za účelem identifikace všech entit odstranit a potom je nutné odstranit každou starou entitu. Můžete snížit počet zpátečních cest na server potřebný k odstranění starých entit dávkováním více požadavků na odstranění do EGTs.  

### <a name="solution"></a>Řešení
Pro každý den pokusů o přihlášení použijte samostatnou tabulku. Pomocí výše uvedeného návrhu entity se můžete vyhnout aktivním oblastem při vkládání entit a odstranění starých entit je nyní jednoduše otázkou odstranění jedné tabulky každý den (jedna operace úložiště) namísto vyhledání a odstranění stovek a tisíců jednotlivých přihlašovacích údajů každý den.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje váš návrh jiné způsoby, jak bude aplikace používat data, jako je například vyhledání konkrétních entit, propojení s jinými daty nebo generování souhrnných informací?  
* Vyhýbá se váš návrh horkým místům při vkládání nových entit?  
* Očekávejte zpoždění, pokud chcete znovu použít stejný název tabulky po jeho odstranění. Je lepší vždy používat jedinečné názvy tabulek.  
* Očekávejte některé omezení při prvním použití nové tabulky, zatímco služba Table se učí vzory přístupu a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často je třeba vytvořit nové tabulky.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud máte velký objem entit, které je nutné odstranit současně.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit
* [Úprava entit](#modifying-entities)  

## <a name="data-series-pattern"></a>Vzor datových řad
Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným scénářem je pro aplikaci pro uložení řady dat, které obvykle potřebuje k načtení všechny najednou. Aplikace může například zaznamenat, kolik zpráv rychlé zprávy každý zaměstnanec odesílá každou hodinu a potom pomocí těchto informací vykreslit, kolik zpráv každý uživatel odeslal za předchozích 24 hodin. Jeden návrh může být pro uložení 24 entit pro každého zaměstnance:  

![Uložit 24 entit pro každého zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Pomocí tohoto návrhu můžete snadno vyhledat a aktualizovat entitu, která se aktualizuje pro každého zaměstnance, kdykoli aplikace potřebuje aktualizovat hodnotu počtu zpráv. Chcete-li však načíst informace k vykreslení grafu aktivity za předchozích 24 hodin, je nutné načíst 24 entit.  

### <a name="solution"></a>Řešení
Použijte následující návrh se samostatnou vlastností pro uložení počtu zpráv pro každou hodinu:  

![Entita Statistiky zpráv](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Pomocí tohoto návrhu můžete použít operaci sloučení k aktualizaci počtu zpráv pro zaměstnance pro určitou hodinu. Nyní můžete načíst všechny informace, které potřebujete k vykreslení grafu pomocí požadavku na jednu entitu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud se celá datová řada nevejde do jedné entity (entita může mít až 252 vlastností), použijte alternativní úložiště dat, jako je například objekt blob.  
* Pokud máte více klientů, kteří aktualizují entitu současně, budete muset použít **eTag** k implementaci optimistické souběžnosti. Pokud máte mnoho klientů, může dojít k vysoké sváru.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete aktualizovat a načíst datovou řadu přidruženou k jednotlivé entitě.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučit nebo nahradit](#merge-or-replace)  
* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern) (pokud ukládáte datové řady v objektu blob)  

## <a name="wide-entities-pattern"></a>Vzor širokých entit
K ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže mít více než 252 vlastností (s výjimkou povinných vlastností systému) a nemůže uložit více než 1 MB dat celkem. V relační databázi by obvykle získat zaokrouhlit všechna omezení na velikost řádku přidáním nové tabulky a vynucení 1-to-1 vztah mezi nimi.  

### <a name="solution"></a>Řešení
Pomocí služby Table můžete uložit více entit představujících jeden velký obchodní objekt s více než 252 vlastnostmi. Chcete-li například uložit počet zpráv rychlé ho schrátosti odeslaných jednotlivými zaměstnanci za posledních 365 dní, můžete použít následující návrh, který používá dvě entity s různými schématy:  

![Více entit](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obou entit, aby byly synchronizovány mezi sebou, můžete použít EGT. V opačném případě můžete použít jednu operaci sloučení k aktualizaci počtu zpráv pro určitý den. Chcete-li načíst všechna data pro jednotlivé zaměstnance, musíte načíst obě entity, které můžete provést se dvěma efektivními požadavky, které používají **hodnotu PartitionKey** i **RowKey.**  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Načtení úplné logické entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení každé fyzické entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte v případě, že potřebujete ukládat entity, jejichž velikost nebo počet vlastností překračuje limity pro jednotlivou entitu ve službě Table.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit
* [Sloučit nebo nahradit](#merge-or-replace)

## <a name="large-entities-pattern"></a>Vzor velkých entit
Úložiště objektů blob slouží k ukládání velkých hodnot vlastností.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže uložit celkem více než 1 MB dat. Pokud jedna nebo několik vlastností ukládá hodnoty, které způsobí, že celková velikost entity překročí tuto hodnotu, nelze uložit celou entitu do služby Tabulka.  

### <a name="solution"></a>Řešení
Pokud vaše entita přesahuje velikost 1 MB, protože jedna nebo více vlastností obsahuje velké množství dat, můžete uložit data ve službě Blob a potom uložit adresu objektu blob ve vlastnosti v entitě. Můžete například uložit fotografii zaměstnance do úložiště objektů blob a uložit odkaz na fotografii ve vlastnosti **Fotografie** vaší entity zaměstnance:  

![Vlastnost fotografie](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zachovat konečnou konzistenci mezi entitou ve službě Table service a daty ve službě Blob, použijte k údržbě entit [vzor Nakonec konzistentní transakce.](#eventually-consistent-transactions-pattern)
* Načtení úplné entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení entity a jednu pro načtení dat objektu blob.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento vzor použijte, pokud potřebujete uložit entity, jejichž velikost překračuje limity pro jednotlivou entitu ve službě Tabulka.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Nakonec konzistentní transakce vzor](#eventually-consistent-transactions-pattern)  
* [Vzor širokých entit](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Předřadit/připojit anti-vzor
Zvyšte škálovatelnost, pokud máte velký objem břitových destiček rozprostřením břitových destiček mezi více oddílů.  

### <a name="context-and-problem"></a>Kontext a problém
Přednastavené nebo připojené entity k uloženým entitám obvykle vede k tomu, že aplikace přidá nové entity do prvního nebo posledního oddílu posloupnosti oddílů. V tomto případě všechny vloží v daném okamžiku probíhají ve stejném oddílu, vytvoření aktivního bodu, který brání službě table service z vyrovnávání zatížení vloží přes více uzlů a může způsobit, že vaše aplikace zasáhnout cíle škálovatelnosti pro Oddíl. Máte-li například aplikaci, která zaznamenává přístup zaměstnanců k síti a prostředkům, může struktura entity, jak je znázorněno níže, vést k tomu, že se oddíl aktuální hodiny stane aktivním bodem, pokud objem transakcí dosáhne cíle škálovatelnosti pro individuální oddíl:  

![Struktura entity](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Řešení
Následující struktura alternativní entity se vyhýbá hotspot u libovolného oddílu jako aplikace protokoluje události:  

![Alternativní struktura účetní jednotky](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Všimněte si, že v tomto příkladu jsou složené klíče **kláves partitionkey** i **RowKey.** **PartitionKey** používá oddělení i ID zaměstnance k distribuci protokolování mezi více oddílů.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje alternativní struktura klíče, která zabraňuje vytváření horkých oddílů v břitových destiček, efektivně dotazy, které provádí klientská aplikace?  
* Znamená očekávaný objem transakcí, že pravděpodobně dosáhnete cílů škálovatelnosti pro jednotlivé oddíly a budete omezeni službou úložiště?  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Vyhněte se předřadit/připojit anti-pattern, pokud váš objem transakcí je pravděpodobné, že povede k omezení službou úložiště při přístupu k aktivní oddíl.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzorek ocasu protokolu](#log-tail-pattern)  
* [Úprava entit](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Protokolovat data anti-pattern
Obvykle byste měli použít službu blob namísto table service k ukládání dat protokolu.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným případem použití dat protokolu je načtení výběru položek protokolu pro určité časové období: například chcete najít všechny chybové a kritické zprávy, které aplikace zaznamenala mezi 15:04 a 15:06 k určitému datu. Nechcete použít datum a čas zprávy protokolu k určení oddílu, který uložíte entity protokolu: to má za následek aktivní oddíl, protože v daném okamžiku budou všechny entity protokolu sdílet stejnou hodnotu **PartitionKey** (viz část [Prepend/append anti-pattern](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu má za následek aktivní oddíl, protože aplikace zapisuje všechny zprávy protokolu do oddílu pro aktuální datum a hodinu:  

![Entita protokolu zpráv](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

V tomto příkladu **RowKey** obsahuje datum a čas zprávy protokolu k zajištění, že zprávy protokolu jsou uloženy seřazené v pořadí data a času a obsahuje ID zprávy v případě, že více zpráv protokolu sdílet stejné datum a čas.  

Dalším přístupem je použití **PartitionKey,** který zajišťuje, že aplikace zapisuje zprávy přes řadu oddílů. Například pokud zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy mezi mnoha oddíly, můžete použít následující schéma entity:  

![Entita alternativní zprávy protokolu](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problém s tímto schématem je však, že chcete-li načíst všechny zprávy protokolu pro určité časové rozpětí, musíte prohledat každý oddíl v tabulce.

### <a name="solution"></a>Řešení
Předchozí část upozornila na problém pokusu o použití služby Table k ukládání položek protokolu a navrhla dva neuspokojivé návrhy. Jedno řešení vedlo k horké oddíl s rizikem špatného výkonu zápisu zpráv protokolu; jiné řešení mělo za následek nízký výkon dotazu z důvodu požadavku na prohledání každého oddílu v tabulce za účelem načtení zpráv protokolu pro určitý časový rozsah. Úložiště objektů blob nabízí lepší řešení pro tento typ scénáře a to je, jak Azure Storage Analytics ukládá data protokolu, které shromažďuje.  

Tato část popisuje, jak Storage Analytics ukládá data protokolu v úložišti objektů blob jako ilustraci tohoto přístupu k ukládání dat, které obvykle dotaz podle rozsahu.  

Storage Analytics ukládá zprávy protokolu ve formátu oddělené ve více objektech BLOB. Formát s oddělovačem usnadňuje klientské aplikaci analyzovat data ve zprávě protokolu.  

Storage Analytics používá konvence pojmenování pro objekty BLOB, které umožňuje vyhledat objektblob (nebo objekty BLOB), které obsahují zprávy protokolu, pro které hledáte. Například objekt blob s názvem "queue/2014/07/31/1800/000001.log" obsahuje zprávy protokolu, které se vztahují ke službě fronty pro hodinu začínající v 18:00 na 31 červenec 2014. "000001" označuje, že se jedná o první soubor protokolu pro toto období. Storage Analytics také zaznamenává časová razítka první a poslední zprávy protokolu uložené v souboru jako součást metadat objektu blob. Rozhraní API pro úložiště objektů blob umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu: chcete-li vyhledat všechny objekty BLOB, které obsahují data protokolu fronty pro hodinu začínající v 18:00, můžete použít předponu "queue/2014/07/31/1800.".  

Služba Storage Analytics interně uchovává zprávy protokolu a poté pravidelně aktualizuje příslušný objekt blob nebo vytvoří nový s nejnovější dávkou položek protokolu. To snižuje počet zápisů, které musí provést do služby objektů blob.  

Pokud implementujete podobné řešení ve vlastní aplikaci, musíte zvážit, jak spravovat kompromis mezi spolehlivostí (zápis každé položky protokolu do úložiště objektů blob, jak se to stane) a náklady a škálovatelnost (ukládání aktualizací do vyrovnávací paměti v aplikaci a psaní ukládání objektů blob v dávkách).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování o způsobu ukládání dat protokolu zvažte následující body:  

* Pokud vytvoříte návrh tabulky, který se vyhýbá potenciální horké oddíly, můžete zjistit, že nelze efektivně přistupovat k datům protokolu.  
* Chcete-li zpracovat data protokolu, klient často potřebuje načíst mnoho záznamů.  
* I když data protokolu je často strukturované, úložiště objektů blob může být lepší řešení.  

## <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé důležité informace, které je třeba mít na paměti při implementaci vzorů popsaných v předchozích částech. Většina této části používá příklady napsané v c#, které používají klientskou knihovnu úložiště (verze 4.3.0 v době psaní).  

## <a name="retrieving-entities"></a>Načítání entit
Jak je popsáno v části Návrh pro dotazování, nejúčinnější dotaz je bodový dotaz. V některých případech však může být nutné načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit pomocí klientské knihovny úložiště.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Spuštění bodového dotazu pomocí klientské knihovny úložiště
Nejjednodušší způsob, jak spustit bodový dotaz, je použít operaci **načíst** tabulku, jak je znázorněno v následujícím fragmentu kódu jazyka C#, který načte entitu s **klávesou PartitionKey** s hodnotou "Prodej" a **RowKey** s hodnotou "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává, že entita, kterou načte, bude typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Načítání více entit pomocí LINQ
Linq můžete použít k načtení více entit ze služby Table při práci se standardní knihovnou tabulky Microsoft Azure Cosmos. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Chcete-li, aby níže uvedené příklady fungovaly, je třeba zahrnout obory názvů:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable je objekt CloudTable, který implementuje metodu CreateQuery\<ITableEntity\<>(), která vrací> TableQuery ITableEntity. Objekty tohoto typu implementují iQueryable a umožňují použití výrazů dotazu LINQ i syntaxe zápisu tečky.

Načítání více entit a dosáhnout zadáním dotazu s **where** klauzule. Chcete-li se vyhnout prohledávací tabulce, měli byste vždy zahrnout hodnotu **PartitionKey** do klauzule where a pokud možno hodnotu **RowKey,** abyste se vyhnuli prohledávačům tabulek a oddílů. Služba table service podporuje omezenou sadu operátorů porovnání (větší než, větší než nebo rovna, menší než, menší než nebo rovna, rovná se a není rovna) pro použití v klauzuli where. 

Následující fragment kódu Jazyka C# vyhledá všechny zaměstnance, jejichž příjmení začíná řetězcem "B" (za předpokladu, že **řádek rowkey** uloží příjmení) v prodejním oddělení (za předpokladu, že **PartitionKey** ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Všimněte si, jak dotaz určuje **RowKey** a **PartitionKey** zajistit lepší výkon.  

Následující ukázka kódu ukazuje ekvivalentní funkce bez použití syntaxe LINQ:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Ukázka vnoří více **CombineFilters** metody zahrnout tři podmínky filtru.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Načítání velkého počtu entit z dotazu
Optimální dotaz vrátí jednotlivé entity na základě **PartitionKey** hodnotu a **RowKey** hodnotu. V některých případech však může mít požadavek vrátit mnoho entit ze stejného oddílu nebo dokonce z mnoha oddílů.  

Vždy byste měli plně otestovat výkon vaší aplikace v těchto scénářích.  

Dotaz proti službě table service může vrátit maximálně 1 000 entit najednou a může být spuštěn maximálně po dobu pěti sekund. Pokud sada výsledků obsahuje více než 1 000 entit, pokud dotaz nebyl dokončen během pěti sekund nebo pokud dotaz překročí hranici oddílu, vrátí služba Table token pokračování, který umožní klientské aplikaci požádat o další sadu entit. Další informace o tom, jak tokeny pokračování fungují, naleznete v [tématu Časový limit dotazu a stránkování](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Pokud používáte klientskou knihovnu úložiště, může automaticky zpracovávat tokeny pokračování za vás, protože vrací entity ze služby Table. Následující ukázka kódu jazyka C# pomocí knihovny klienta úložiště automaticky zpracovává tokeny pokračování, pokud je služba table service vrátí v odpovědi:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Následující kód jazyka C# zpracovává tokeny pokračování explicitně:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Pomocí tokenů pokračování explicitně můžete řídit, kdy vaše aplikace načte další segment dat. Pokud například klientská aplikace umožňuje uživatelům procházet entity uložené v tabulce, může se uživatel rozhodnout, že nebude procházet všechny entity načtené dotazem, takže aplikace použije token pokračování pouze k načtení dalšího segmentu, když uživatel dokončil stránkování přes všechny entity v aktuálním segmentu. Tento přístup má několik výhod:  

* Umožňuje omezit množství dat, která lze načíst ze služby Table service, a přesunutí po síti.  
* Umožňuje provádět asynchronní vi va v .NET.  
* Umožňuje serializovat token pokračování do trvalého úložiště, takže můžete pokračovat v případě selhání aplikace.  

> [!NOTE]
> Token pokračování obvykle vrací segment obsahující 1 000 entit, i když může být méně. To platí také v případě, že omezíte počet položek, které dotaz vrátí pomocí **take,** abyste vrátili první n entity, které odpovídají vašim vyhledávacím kritériím: služba table service může vrátit segment obsahující méně než n entit spolu s tokenem pokračování, který vám umožní načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak změnit počet entit vrácených uvnitř segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít až 255 vlastností a mít velikost až 1 MB. Při dotazování na tabulku a načtení entit, nemusí potřebovat všechny vlastnosti a můžete se vyhnout přenosu dat zbytečně (ke snížení latence a nákladů). Projekce na straně serveru můžete použít k přenosu pouze vlastností, které potřebujete. Následující příklad je načte pouze **Email** vlastnost (spolu s **PartitionKey**, **RowKey**, **Timestamp**, a **ETag**) z entit vybraných dotazem.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Všimněte si, jak **RowKey** hodnota je k dispozici i v případě, že nebyla zahrnuta v seznamu vlastností načíst.  

## <a name="modifying-entities"></a>Úprava entit
Klientská knihovna úložiště umožňuje upravit entity uložené ve službě table service vložením, odstraněním a aktualizací entit. Egts můžete použít k dávkování více vložení, aktualizace a odstranění operace společně snížit počet zpátečních cest požadované a zlepšit výkon vašeho řešení.  

Výjimky vyvolány při spuštění knihovny klienta úložiště EGT obvykle zahrnují index entity, která způsobila selhání dávky. To je užitečné při ladění kódu, který používá EGTs.  

Měli byste také zvážit, jak váš návrh ovlivňuje způsob, jakým klientská aplikace zpracovává souběžnost a operace aktualizace.  

### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení služba table service implementuje optimistické kontroly souběžnosti na úrovni jednotlivých entit pro operace **Vložení**, **Slučování**a **Odstranění,** i když je možné, aby klient vynutit table service tyto kontroly obejít. Další informace o tom, jak služba table service spravuje souběžnost, naleznete [v tématu Správa souběžnosti v úložišti Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Sloučit nebo nahradit
Metoda **Replace** třídy **TableOperation** vždy nahrazuje úplnou entitu ve službě Table Service. Pokud do požadavku nezahrnete vlastnost, pokud tato vlastnost existuje v uložené entitě, požadavek tuto vlastnost odebere z uložené entity. Pokud nechcete explicitně odebrat vlastnost z uložené entity, musíte zahrnout všechny vlastnosti v požadavku.  

Metodu **Merge** třídy **TableOperation** můžete použít ke snížení množství dat, která odesíláte do služby Table, pokud chcete aktualizovat entitu. Metoda **Merge** nahradí všechny vlastnosti v uložené entitě hodnotami vlastností z entity obsažené v požadavku, ale ponechá beze změny všech vlastností v uložené entitě, které nejsou zahrnuty v požadavku. To je užitečné, pokud máte velké entity a stačí aktualizovat malý počet vlastností v požadavku.  

> [!NOTE]
> **Metody Nahradit** a **sloučit** se nezdaří, pokud entita neexistuje. Jako alternativu můžete použít **InsertOrReplace** a **InsertOrMerge** metody, které vytvářejí novou entitu, pokud neexistuje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Práce s heterogenními typy entit
Služba Table je úložiště tabulek *bez* schématu, což znamená, že jedna tabulka může ukládat entity více typů, které poskytují velkou flexibilitu v návrhu. Následující příklad ilustruje tabulku s entitami zaměstnanců i oddělení:  

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
<th>E-mail</th>
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
<th>E-mail</th>
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
<th>DepartmentName</th>
<th>ZaměstnanecCount</th>
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
<th>E-mail</th>
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

Každá entita musí mít stále hodnoty **PartitionKey**, **RowKey**a **Timestamp,** ale může mít libovolnou sadu vlastností. Kromě toho neexistuje nic, co by naznačovalo typ entity, pokud se nerozhodnete tyto informace někam uložit. Existují dvě možnosti pro identifikaci typu entity:  

* Předřadit typ entity na **RowKey** (nebo případně **PartitionKey).** Například **EMPLOYEE_000123** nebo **DEPARTMENT_SALES** jako **RowKey** hodnoty.  
* K zaznamenání typu entity použijte samostatnou vlastnost, jak je znázorněno v následující tabulce.  

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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
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
<th>EntityType</th>
<th>DepartmentName</th>
<th>ZaměstnanecCount</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Věk</th>
<th>E-mail</th>
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

První možnost, prepending typ entity **RowKey**, je užitečné, pokud existuje možnost, že dvě entity různých typů může mít stejnou hodnotu klíče. Také seskupí entity stejného typu společně v oddílu.  

Techniky popsané v této části jsou důležité zejména pro diskuse [vztahy dědičnosti](table-storage-design-modeling.md#inheritance-relationships) dříve v této příručce v článku [Modelování vztahy](table-storage-design-modeling.md).  

> [!NOTE]
> Měli byste zvážit zahrnutí čísla verze do hodnoty typu entity, aby klientské aplikace mohly vyvíjet objekty POCO a pracovat s různými verzemi.  
> 
> 

Zbývající část této části popisuje některé funkce v klientské knihovně úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

### <a name="retrieving-heterogeneous-entity-types"></a>Načítání heterogenních typů entit
Pokud používáte klientskou knihovnu úložiště, máte tři možnosti pro práci s více typy entit.  

Pokud znáte typ entity uložené s konkrétními hodnotami **RowKey** a **PartitionKey,** můžete zadat typ entity při načtení entity, jak je znázorněno v předchozích dvou příkladech, které načítají entity typu **EmployeeEntity**: [Spuštění bodového dotazu pomocí klientské knihovny úložiště](#executing-a-point-query-using-the-storage-client-library) a [načítání více entit pomocí LINQ](#retrieving-multiple-entities-using-linq).  

Druhou možností je použít typ **DynamicTableEntity** (taška vlastností) namísto konkrétního typu entity POCO (tato možnost může také zlepšit výkon, protože není nutné serializovat a rekonstruovat entity na typy .NET). Následující kód jazyka C# potenciálně načte více entit různých typů z tabulky, ale vrátí všechny entity jako instance **DynamicTableEntity.** Potom používá **Vlastnost EntityType** k určení typu každé entity:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
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
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Chcete-li načíst další vlastnosti, musíte použít metodu **TryGetValue** ve **vlastnosti Vlastnosti** třídy **DynamicTableEntity.**  

Třetí možností je kombinovat pomocí **DynamicTableEntity** typu a **EntityResolver** instance. To umožňuje vyřešit více typů POCO ve stejném dotazu. V tomto příkladu **ententresolver** delegát používá **EntityType** vlastnost rozlišovat mezi dvěma typy entity, které vrátí dotaz. Metoda **Resolve** používá delegáta **překládání** k překladu instancí **DynamicTableEntity** na instance **TableEntity.**  

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
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Úprava heterogenních typů entit
K odstranění entity nemusíte znát typ entity a vždy znáte typ entity při vložení. Typ **DynamicTableEntity** však můžete použít k aktualizaci entity bez znalosti jejího typu a bez použití třídy entity POCO. Následující ukázka kódu načte jednu entitu a zkontroluje, zda vlastnost **EmployeeCount** existuje před její aktualizací.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Řízení přístupu pomocí sdílených přístupových podpisů
Tokeny sdíleného přístupového podpisu (SAS) můžete klientům umožnit upravovat (a dotazovat) entity tabulky bez nutnosti zahrnout klíč účtu úložiště do kódu. Obvykle existují tři hlavní výhody používání SAS ve vaší aplikaci:  

* Není nutné distribuovat klíč účtu úložiště na nezabezpečenou platformu (například mobilní zařízení), aby toto zařízení mohlo přistupovat k entitám ve službě Table a upravovat je.  
* Můžete převést část práce, kterou webové a pracovní role provádějí při správě entit, na klientská zařízení, jako jsou počítače koncových uživatelů a mobilní zařízení.  
* Klientovi můžete přiřadit omezenou a časově omezenou sadu oprávnění (například povolit přístup jen pro čtení k určitým prostředkům).  

Další informace o používání tokenů SAS se službou Table naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](../../storage/common/storage-sas-overview.md)  

Je však nutné stále generovat tokeny SAS, které udělují klientskou aplikaci entitám ve službě table service: měli byste to provést v prostředí, které má zabezpečený přístup ke klíčům účtu úložiště. Obvykle používáte webovou nebo pracovní roli ke generování tokenů SAS a jejich doručení do klientských aplikací, které potřebují přístup k vašim entitám. Vzhledem k tomu, že stále existuje režie, která se podílí na generování a doručování tokenů SAS klientům, měli byste zvážit, jak nejlépe snížit tuto režii, zejména ve scénářích s velkým objemem.  

Je možné generovat token SAS, který uděluje přístup k podmnožině entit v tabulce. Ve výchozím nastavení vytvoříte token SAS pro celou tabulku, ale je také možné určit, že token SAS udělí přístup k rozsahu hodnot **PartitionKey** nebo rozsahu hodnot **PartitionKey** a **RowKey.** Můžete se rozhodnout generovat tokeny SAS pro jednotlivé uživatele vašeho systému tak, aby token SAS každého uživatele umožňoval pouze přístup k vlastním entitám ve službě table service.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Za předpokladu, že rozšiřujete požadavky na více oddílů, můžete zlepšit propustnost a odezvu klienta pomocí asynchronních nebo paralelních dotazů.
Můžete mít například dvě nebo více instancí role pracovní ho distančního procesu, které přistupují k tabulkám paralelně. Můžete mít jednotlivé role pracovního procesu odpovědné za určité sady oddílů nebo jednoduše mít více instancí rolí pracovního procesu, z nichž každá má přístup ke všem oddílům v tabulce.  

V rámci instance klienta můžete zlepšit propustnost spuštěním operací úložiště asynchronně. Klientská knihovna úložiště usnadňuje psaní asynchronních dotazů a úprav. Můžete například začít synchronní metodou, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu jazyka C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Tento kód můžete snadno upravit tak, aby dotaz spouští asynchronně následujícím způsobem:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

V tomto asynchronním příkladu se zobrazí následující změny ze synchronní verze:  

* Podpis metody nyní obsahuje **asynchronní** modifikátor a vrátí instanci **Úlohy.**  
* Místo volání metody **ExecuteSegmented** k načtení výsledků nyní metoda volá metodu **ExecuteSegmentedAsync** a používá modifikátor **await** k asynchronnímu načtení výsledků.  

Klientská aplikace může volat tuto metodu vícekrát (s různými hodnotami pro parametr **oddělení)** a každý dotaz bude spuštěn v samostatném vlákně.  

Neexistuje žádná asynchronní verze metody **Execute** ve třídě **TableQuery,** protože rozhraní **IEnumerable** nepodporuje asynchronní výčet.  

Entity můžete také asynchronně vkládat, aktualizovat a odstraňovat. Následující příklad jazyka C# ukazuje jednoduchou synchronní metodu vložení nebo nahrazení entity zaměstnance:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Tento kód můžete snadno upravit tak, aby aktualizace schovaná asynchronně následujícím způsobem:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto asynchronním příkladu se zobrazí následující změny ze synchronní verze:  

* Podpis metody nyní obsahuje **asynchronní** modifikátor a vrátí instanci **Úlohy.**  
* Místo volání **Execute** metoda aktualizovat entitu, metoda nyní volá **ExecuteAsync** metoda a používá modifikátor **await** načíst výsledky asynchronně.  

Klientská aplikace může volat více asynchronních metod, jako je tento, a každé vyvolání metody bude spuštěno v samostatném vlákně.  

## <a name="next-steps"></a>Další kroky

- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
