---
title: Vzory návrhu tabulky Azure Storage | Microsoft Docs
description: Projděte si vzory návrhu, které jsou vhodné pro použití s Table service řešení v Azure. Vyřešte problémy a kompromisy, které jsou popsány v jiných článcích.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 2eb109078728b8a9070b3991733450c1da790d9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879591"
---
# <a name="table-design-patterns"></a>Způsoby návrhu tabulek
Tento článek popisuje některé vzory vhodné pro použití s Table service řešení. Také se dozvíte, jak můžete prakticky vyřešit některé problémy a kompromisy popsané v dalších článcích o návrhu úložiště tabulek. Následující diagram shrnuje vztahy mezi různými vzory:  

![vyhledání souvisejících dat](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mapa vzorů výše ukazuje několik vztahů mezi vzory (modrý) a antipatterns (oranžová), které jsou popsány v tomto průvodci. Existuje mnoho dalších vzorů, které je potřeba zvážit. Jedním z klíčových scénářů pro službu Table Service je například použití [schématu materializované zobrazení](/previous-versions/msp-n-p/dn589782(v=pandp.10)) ze vzoru [dělení zodpovědnosti (CQRS) dotazu příkazu](/previous-versions/msp-n-p/jj554200(v=pandp.10)) .  

## <a name="intra-partition-secondary-index-pattern"></a>Vzor sekundárního indexu v rámci oddílu
Pomocí různých hodnot **RowKey** (ve stejném oddílu) můžete ukládat víc kopií každé entity, aby bylo možné rychle a efektivně vyhledávat a alternativní objednávky řazení pomocí různých hodnot **RowKey** . Aktualizace mezi kopiemi se můžou uchovávat konzistentně pomocí EGTs.  

### <a name="context-and-problem"></a>Kontext a problém
Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey** . To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí struktury tabulky zobrazené níže může klientská aplikace použít dotaz na bod k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty **PartitionKey** a **RowKey** ). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Pokud chcete také vyhledat entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, je třeba použít méně efektivní kontrolu oddílů a vyhledat shodu. Důvodem je, že služba Table Service neposkytuje sekundární indexy. Kromě toho neexistuje možnost požadovat seznam zaměstnanců seřazených v jiném pořadí než **RowKey** objednávka.  

### <a name="solution"></a>Řešení
Pokud chcete obejít nedostatku sekundárních indexů, můžete pro každou kopii ukládat několik kopií pomocí jiné hodnoty **RowKey** . Pokud uložíte entitu se strukturami uvedenými níže, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro **RowKey**, "empid_" a "email_" umožňují dotazovat se na jednoho zaměstnance nebo na určitou škálu zaměstnanců pomocí rozsahu e-mailových adres nebo ID zaměstnanců.  

![Entity zaměstnanců](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Následující dvě kritéria filtru (jedna hledají podle ID zaměstnance a jedna při hledání e-mailové adresy) určují obě dotazy na bod:  

* $filter = (PartitionKey EQ ' Sales ') a (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') a (RowKey EQ ' email_jonesj@contoso.com ')  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese pomocí dotazu na entity s příslušnou předponou v **RowKey**.  

* Chcete-li najít všechny zaměstnance v prodejním oddělení s ID zaměstnance v rozsahu 000100 až 000199 použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' empid_000100 ') a (RowKey Le ' empid_000199 ')  
* Pokud chcete najít všechny zaměstnance v prodejním oddělení s e-mailovou adresou začínající písmenem "a", použijte: $filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' email_a ') a (RowKey lt ' email_b ')  
  
  Syntaxe filtru použitá ve výše uvedených příkladech je z REST API Table service, další informace najdete v tématu věnovaném [dotazům k entitě](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Úložiště tabulek je poměrně levné na použití, takže režijní náklady na ukládání duplicitních dat by neměly být zásadním problémem. Měli byste ale vždycky vyhodnotit náklady na váš návrh na základě předpokládaných požadavků na úložiště a přidávat duplicitní entity jenom na podporu dotazů, které klientská aplikace spustí.  
* Vzhledem k tomu, že se entity sekundárního indexu ukládají do stejného oddílu jako původní entity, měli byste zajistit, aby nedošlo k překročení cílů škálovatelnosti pro jednotlivé oddíly.  
* Duplicitní entity můžete udržovat vzájemně konzistentní pomocí EGTs k aktualizaci dvou kopií této entity. To znamená, že byste měli ukládat všechny kopie entity do stejného oddílu. Další informace najdete v části [použití transakcí skupin entit](table-storage-design.md#entity-group-transactions).  
* Hodnota použitá pro **RowKey** musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Vyplňování číselných hodnot v **RowKey** (například ID zaměstnance 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Například pokud dotazy, které hledají entity pomocí e-mailové adresy ve **RowKey** , nikdy nepotřebují věk zaměstnance, můžou mít tyto entity následující strukturu:

   ![Struktura entit zaměstnanců](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Obvykle je lepší ukládat duplicitní data a zajistit, že můžete načíst všechna data, která potřebujete, pomocí jediného dotazu, než pomocí jednoho dotazu vyhledat entitu a druhou pro vyhledání požadovaných dat.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že klientská aplikace potřebuje načítat entity pomocí různých klíčů, když klient potřebuje načíst entity v různých objednávkách řazení a kde můžete identifikovat každou entitu pomocí různých jedinečných hodnot. Měli byste ale pozor, abyste při provádění vyhledávání entit nepřekročili omezení škálovatelnosti oddílu, a to pomocí různých hodnot **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor sekundárního indexu mezi oddíly](#inter-partition-secondary-index-pattern)
* [Vzor složeného klíče](#compound-key-pattern)
* Transakce skupiny entit
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Vzor sekundárního indexu mezi oddíly
V samostatných oddílech nebo v samostatných tabulkách můžete ukládat víc kopií každé entity pomocí různých **RowKey** hodnot a povolit rychlé a efektivní vyhledávání a alternativní objednávky řazení pomocí různých hodnot **RowKey** .  

### <a name="context-and-problem"></a>Kontext a problém
Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey** . To umožňuje klientské aplikaci efektivně načíst entitu pomocí těchto hodnot. Například pomocí struktury tabulky zobrazené níže může klientská aplikace použít dotaz na bod k načtení konkrétní entity zaměstnance pomocí názvu oddělení a ID zaměstnance (hodnoty **PartitionKey** a **RowKey** ). Klient může také načíst entity seřazené podle ID zaměstnance v rámci každého oddělení.  

![ID zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Pokud chcete také vyhledat entitu zaměstnance na základě hodnoty jiné vlastnosti, jako je například e-mailová adresa, je třeba použít méně efektivní kontrolu oddílů a vyhledat shodu. Důvodem je, že služba Table Service neposkytuje sekundární indexy. Kromě toho neexistuje možnost požadovat seznam zaměstnanců seřazených v jiném pořadí než **RowKey** objednávka.  

Očekáváte velký objem transakcí na těchto entitách a chcete minimalizovat riziko Table service omezování klienta.  

### <a name="solution"></a>Řešení
Pokud chcete obejít nedostatku sekundárních indexů, můžete pro každou kopii uložit více kopií jednotlivých entit pomocí různých hodnot **PartitionKey** a **RowKey** . Pokud uložíte entitu se strukturami uvedenými níže, můžete efektivně načíst entity zaměstnanců na základě e-mailové adresy nebo ID zaměstnance. Hodnoty předpony pro **PartitionKey**, "empid_" a "email_" umožňují určit, který index chcete použít pro dotaz.  

![Primární index a sekundární index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Následující dvě kritéria filtru (jedna hledají podle ID zaměstnance a jedna při hledání e-mailové adresy) určují obě dotazy na bod:  

* $filter = (PartitionKey EQ ' empid_Sales ') a (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') a (RowKey EQ ' jonesj@contoso.com ')  

Pokud se dotazuje na rozsah entit zaměstnanců, můžete určit rozsah seřazený v pořadí podle ID zaměstnance nebo rozsah seřazený v e-mailové adrese pomocí dotazu na entity s příslušnou předponou v **RowKey**.  

* Chcete-li najít všechny zaměstnance v rámci prodejního oddělení s ID zaměstnance v rozsahu **000100** až **000199** seřazený v pořadí podle ID zaměstnanců, použijte: $Filter = (PartitionKey EQ ' empid_Sales ') a (RowKey GE ' 000100 ') a (RowKey Le ' 000199 ')  
* Pokud chcete najít všechny zaměstnance v prodejním oddělení pomocí e-mailové adresy, která začíná na a seřazená v e-mailové adrese, použijte: $filter = (PartitionKey EQ ' email_Sales ') a (RowKey GE ' a ') a (RowKey lt ' b ')  

Syntaxe filtru použitá ve výše uvedených příkladech je z REST API Table service, další informace najdete v tématu věnovaném [dotazům k entitě](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* V případě, že jsou vaše duplicitní entity trvale konzistentní, pomocí [vzoru nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) Udržujte entity primárního a sekundárního indexu.  
* Úložiště tabulek je poměrně levné na použití, takže režijní náklady na ukládání duplicitních dat by neměly být zásadním problémem. Měli byste ale vždycky vyhodnotit náklady na váš návrh na základě předpokládaných požadavků na úložiště a přidávat duplicitní entity jenom na podporu dotazů, které klientská aplikace spustí.  
* Hodnota použitá pro **RowKey** musí být jedinečná pro každou entitu. Zvažte použití hodnot složených klíčů.  
* Vyplňování číselných hodnot v **RowKey** (například ID zaměstnance 000223) umožňuje správné řazení a filtrování na základě horních a dolních mezí.  
* Nemusíte nutně Duplikovat všechny vlastnosti vaší entity. Například pokud dotazy, které hledají entity pomocí e-mailové adresy ve **RowKey** , nikdy nepotřebují věk zaměstnance, můžou mít tyto entity následující strukturu:
  
   ![Entita zaměstnance (sekundární index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Obvykle je lepší ukládat duplicitní data a zajistit, že můžete načíst všechna data, která potřebujete, pomocí jediného dotazu, než můžete použít jeden dotaz k vyhledání entity pomocí sekundárního indexu a další pro vyhledání požadovaných dat v primárním indexu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že klientská aplikace potřebuje načítat entity pomocí různých klíčů, když klient potřebuje načíst entity v různých objednávkách řazení a kde můžete identifikovat každou entitu pomocí různých jedinečných hodnot. Tento model použijte, pokud chcete zabránit překročení omezení škálovatelnosti oddílu při provádění vyhledávání entit pomocí různých hodnot **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* [Vzor sekundárního indexu v rámci oddílu](#intra-partition-secondary-index-pattern)  
* [Vzor složeného klíče](#compound-key-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Vzor nakonec konzistentních transakcí
Pomocí front Azure povolte nakonec konzistentní chování napříč hranicemi oddílů nebo hranicemi systému úložiště.  

### <a name="context-and-problem"></a>Kontext a problém
EGTs umožňují atomické transakce napříč několika entitami, které sdílejí stejný klíč oddílu. V zájmu výkonu a škálovatelnosti se můžete rozhodnout ukládat entity, které mají požadavky na konzistenci v samostatných oddílech nebo v samostatném systému úložiště: v takovém případě nemůžete použít EGTs k zachování konzistence. Například můžete mít požadavek na udržení konečné konzistence mezi:  

* Entity uložené ve dvou různých oddílech ve stejné tabulce, v různých tabulkách nebo v různých účtech úložiště.  
* Entita uložená v Table service a objekt BLOB uložený v Blob service.  
* Entita uložená v Table service a soubor v systému souborů.  
* Entita uložená v Table service ještě byla indexována pomocí služby Azure Kognitivní hledání.  

### <a name="solution"></a>Řešení
Pomocí front Azure můžete implementovat řešení, které poskytuje konečnou konzistenci ve dvou nebo více oddílech nebo systémech úložišť.
Pro ilustraci tohoto přístupu Předpokládejme, že máte požadavek, aby bylo možné archivovat staré entity zaměstnanců. Staré entity zaměstnanců se dotazují zřídka a měly by se vyloučit ze všech aktivit, které se týkají současných zaměstnanců. K implementaci tohoto požadavku ukládáte aktivní zaměstnance do **aktuální** tabulky a staré zaměstnance v **archivní** tabulce. Archivace zaměstnance vyžaduje, abyste odstranili entitu z **aktuální** tabulky a přidali entitu do tabulky **archivu** , ale nemůžete použít EGT k provedení těchto dvou operací. Aby nedocházelo k riziku, že selhání způsobilo, že se entita objevila v obou nebo ani v tabulkách, musí být operace archivu nakonec konzistentní. Následující sekvenční diagram popisuje kroky v této operaci. Další podrobnosti jsou k dispozici pro cesty výjimek v následujícím textu.  

![Řešení front Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient inicializuje operaci archivu tím, že umístí zprávu do fronty Azure, v tomto příkladu bude archivován #456 zaměstnanců. Role pracovního procesu se dotazuje fronty na nové zprávy; Když ho najde, přečte zprávu a ponechá ve frontě skrytou kopii. Role pracovního procesu Next načte kopii entity z **aktuální** tabulky, vloží kopii do **archivní** tabulky a odstraní původní z **aktuální** tabulky. Nakonec, pokud z předchozích kroků nedošlo k chybám, role pracovního procesu odstraní skrytou zprávu z fronty.  

V tomto příkladu krok 4 vloží zaměstnance do **archivní** tabulky. Může přidat zaměstnance do objektu BLOB v Blob service nebo v souboru v systému souborů.  

### <a name="recovering-from-failures"></a>Obnovování při selhání
Je důležité, aby operace v krocích **4** a **5** byly *idempotentní* v případě, že role pracovního procesu potřebuje restartovat operaci archivace. Pokud používáte Table service, v kroku **4** byste měli použít operaci vložení nebo nahrazení; v kroku **5** byste měli použít operaci odstranit, pokud existuje, v klientské knihovně, kterou používáte. Pokud používáte jiný systém úložiště, je nutné použít příslušnou operaci idempotentní.  

Pokud role pracovního procesu nikdy nedokončí krok **6**, potom po vypršení časového limitu se zpráva znovu zobrazí ve frontě připravené pro roli pracovního procesu a pokusí se ji znovu zpracovat. Role pracovního procesu může kontrolovat počet čtení zprávy ve frontě a v případě potřeby označit jako "nezpracovatelnou" zprávu pro účely šetření odesláním do samostatné fronty. Další informace o čtení zpráv fronty a o kontrole počtu vyřazování z fronty najdete v tématu [Get Messages](/rest/api/storageservices/Get-Messages).  

Některé chyby ze služby Table a Queue jsou přechodnými chybami a klientská aplikace by měla k jejich zpracování použít vhodnou logiku opakování.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení neposkytuje izolaci transakcí. Například klient může číst **aktuální** a **archivní** tabulky, pokud byla role pracovního procesu mezi kroky **4** a **5** a zobrazit nekonzistentní zobrazení dat. Data budou nakonec konzistentní.  
* Musíte mít jistotu, že kroky 4 a 5 jsou idempotentní, aby se zajistila konečná konzistence.  
* Řešení můžete škálovat pomocí několika front a instancí rolí pracovního procesu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud chcete zaručit konečnou konzistenci mezi entitami, které existují v různých oddílech nebo tabulkách. Tento model můžete roztáhnout, aby se zajistila konečná konzistence operací v rámci Table service a Blob service a jiných neAzure Storagech datových zdrojů, jako je databáze nebo systém souborů.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit  
* [Sloučit nebo nahradit](#merge-or-replace)  

> [!NOTE]
> Pokud je pro vaše řešení důležité oddělení transakcí, měli byste zvážit změnu návrhu tabulek, abyste mohli používat EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Vzor entit indexu
Udržujte entity indexu, abyste umožnili efektivní hledání, které vrací seznam entit.  

### <a name="context-and-problem"></a>Kontext a problém
Table service automaticky indexuje entity pomocí hodnot **PartitionKey** a **RowKey** . To umožňuje klientské aplikaci efektivně načíst entitu pomocí dotazu na bod. Například pomocí struktury tabulky zobrazené níže může klientská aplikace efektivně načíst jednotlivou entitu zaměstnance pomocí názvu oddělení a ID zaměstnance ( **PartitionKey** a **RowKey**).  

![Entita zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Pokud chcete mít také přístup k seznamu entit zaměstnanců v závislosti na hodnotě jiné nejedinečné vlastnosti, jako je například jejich příjmení, je nutné použít méně efektivní kontrolu oddílu k vyhledání shody namísto použití indexu k jejich přímému vyhledání. Důvodem je, že služba Table Service neposkytuje sekundární indexy.  

### <a name="solution"></a>Řešení
Chcete-li povolit vyhledávání podle příjmení pomocí struktury entity uvedené výše, je nutné udržovat seznamy ID zaměstnanců. Pokud chcete načíst entity zaměstnanců s konkrétním názvem, jako je například Novotný, musíte nejprve vyhledat seznam ID zaměstnanců pro zaměstnance s názvem Novotný jako své příjmení a pak tyto entity zaměstnanců načíst. Existují tři hlavní možnosti ukládání seznamů ID zaměstnanců:  

* Použijte úložiště objektů BLOB.  
* Vytvořte entity indexu ve stejném oddílu jako entity zaměstnanců.  
* Vytvořte entity indexu v samostatném oddílu nebo tabulce.  

<u>Možnost #1: použití BLOB Storage</u>  

Pro první možnost vytvoříte objekt BLOB pro každé jedinečné příjmení a v každém objektu BLOB uložíte seznam hodnot **PartitionKey** (oddělení) a **RowKey** (ID zaměstnance) pro zaměstnance s tímto posledním jménem. Když zaměstnance přidáte nebo odstraníte, měli byste zajistit, aby obsah relevantního objektu BLOB byl nakonec konzistentní s entitami zaměstnanců.  

<u>Možnost #2:</u> Vytvoření entit indexu ve stejném oddílu  

Pro druhou možnost použijte entity indexů, které ukládají následující data:  

![Entita indexu zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Vlastnost **číslozaměstnances** obsahuje seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v **RowKey**.  

Následující kroky popisují postup, který byste měli provést při přidávání nového zaměstnance, pokud používáte druhou možnost. V tomto příkladu přidáme zaměstnance s ID 000152 a příjmením Novotný do oddělení Sales:  

1. Načte entitu indexu s hodnotou **PartitionKey** "Sales" a hodnotou **RowKey** "Novotný". Uložte značku ETag této entity, která se použije v kroku 2.  
2. Vytvořte transakci skupiny entit (tj. operaci dávky), která vloží novou entitu zaměstnanec (**PartitionKey** value "Sales" and **RowKey** value "000152") a aktualizuje entitu indexu (**PartitionKey** value "Sales" a **RowKey** value "Novák") přidáním nového zaměstnance do seznamu v poli ČísloZaměstnance. Další informace o transakcích skupin entit najdete v tématu transakce skupin entit.  
3. Pokud transakce skupiny entit selhává z důvodu chyby optimistického souběhu (někdo jiný právě změnil entitu indexu), pak je potřeba začít znovu v kroku 1.  

Pokud používáte druhou možnost, můžete použít podobný přístup k odstranění zaměstnance. Změna příjmení zaměstnance je mírně složitější, protože budete muset spustit transakci skupiny entit, která aktualizuje tři entity: entitu zaměstnanec, entitu indexu pro staré příjmení a entitu indexu pro nový poslední název. Musíte načíst každou entitu před provedením jakýchkoli změn, aby bylo možné načíst hodnoty ETag, které pak můžete použít k provedení aktualizací pomocí optimistické souběžnosti.  

Následující kroky popisují postup, který byste měli provést, pokud potřebujete vyhledat všechny zaměstnance s daným posledním jménem v oddělení, pokud používáte druhou možnost. V tomto příkladu vyhledáváme všechny zaměstnance s názvem Novotný v oddělení Sales (prodej):  

1. Načte entitu indexu s hodnotou **PartitionKey** "Sales" a hodnotou **RowKey** "Novotný".  
2. Analyzovat seznam ID zaměstnanců v poli ČísloZaměstnance.  
3. Pokud potřebujete další informace o každém z těchto zaměstnanců (například jejich e-mailové adresy), načtěte každou entitu zaměstnanců pomocí hodnot **PartitionKey** Sales a **RowKey** ze seznamu zaměstnanců, který jste získali v kroku 2.  

<u>Možnost #3:</u> Vytváření entit indexu v samostatném oddílu nebo tabulce  

U třetí možnosti použijte entity indexů, které ukládají následující data:  

![Entita indexu zaměstnance v samostatném oddílu](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Vlastnost **číslozaměstnances** obsahuje seznam ID zaměstnanců pro zaměstnance s posledním jménem uloženým v **RowKey**.  

Třetí možností je, že nemůžete pomocí EGTs zachovat konzistenci, protože entity indexu jsou v samostatném oddílu od entit zaměstnanců. Zajistěte, aby entity indexu byly nakonec konzistentní s entitami zaměstnanců.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Toto řešení vyžaduje aspoň dva dotazy, aby se načetly vyhovující entity: jednu pro dotazování entit indexu pro získání seznamu hodnot **RowKey** a pak dotazy pro načtení jednotlivých entit v seznamu.  
* Vzhledem k tom, že jednotlivá entita má maximální velikost 1 MB, možnost #2 a možnost #3 v řešení předpokládá, že seznam identifikátorů zaměstnanců pro jakékoli křestní jméno nebude nikdy větší než 1 MB. Pokud je seznam ID zaměstnanců pravděpodobně větší než 1 MB, použijte možnost #1 a uložte data indexu do úložiště objektů BLOB.  
* Pokud použijete možnost #2 (s použitím EGTs ke zpracování přidávání a odstraňování zaměstnanců a změně příjmení zaměstnance), musíte vyhodnotit, jestli se objem transakcí bude přicházet k omezením škálovatelnosti v daném oddílu. Pokud se jedná o tento případ, měli byste zvážit vhodné řešení (možnost #1 nebo možnost #3), které používá fronty ke zpracování žádostí o aktualizaci, a umožňuje ukládat entity indexu do samostatného oddílu od entit zaměstnanců.  
* Možnost #2 v tomto řešení předpokládá, že chcete hledat podle příjmení v rámci oddělení: například chcete načíst seznam zaměstnanců s posledním názvem Novotný v oddělení sales. Pokud chcete být schopni vyhledat všechny zaměstnance s posledním názvem Novotný v celé organizaci, použijte jednu z možností #1 nebo #3 možnosti.
* Můžete implementovat řešení založené na frontách, které poskytuje konečnou konzistenci (Další informace najdete ve [vzoru nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) ).  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že chcete vyhledat sadu entit, které budou sdílet všechny společné hodnoty vlastností, jako jsou všichni zaměstnanci s posledním názvem Novotný.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Vzorek denormalizace
Kombinování souvisejících dat společně v jedné entitě vám umožní načíst všechna potřebná data pomocí dotazu s jedním bodem.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi obvykle Normalizujte data pro odebrání duplicit, což vede k dotazům, které načítají data z více tabulek. Pokud normalizete data v tabulkách Azure, je potřeba provést několik přenosů od klienta k serveru, aby se načetla vaše související data. Například u struktury tabulky zobrazené níže potřebujete dvě zpáteční cesty pro získání podrobností o oddělení: jednu pro načtení entity oddělení, která zahrnuje ID manažera, a potom další požadavek na načtení podrobností správce v entitě Employee.  

![Entita oddělení a entita zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Řešení
Místo uložení dat ve dvou samostatných entitách denormalizujte data a udržujte kopii podrobností manažera v entitě oddělení. Například:  

![Entita oddělení](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

S entitami oddělení uloženými s těmito vlastnostmi teď můžete načíst všechny podrobnosti, které potřebujete o oddělení, pomocí dotazu na bod.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* K ukládání dat dvakrát se účtují nějaké náklady. Výhoda výkonu (z menšího počtu požadavků na službu úložiště) obvykle převažuje nad nárůstem nákladů na úložiště (a tato cena je částečně posunuta snížením počtu transakcí, které vyžadujete k načtení podrobností o oddělení).  
* Je nutné zachovat konzistenci dvou entit, které ukládají informace o manažerech. Problém konzistence můžete zpracovat pomocí EGTs k aktualizaci více entit v jedné atomické transakci: v tomto případě se entita oddělení a entita zaměstnanci pro správce oddělení ukládají do stejného oddílu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte, pokud často potřebujete vyhledat související informace. Tento model snižuje počet dotazů, které musí váš klient učinit, aby mohl načíst data, která vyžaduje.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Vzor složeného klíče
Pomocí složených hodnot **RowKey** můžete klientovi povolit, aby vyhledal související data pomocí dotazu s jedním bodem.  

### <a name="context-and-problem"></a>Kontext a problém
V relační databázi je přirozené použití spojení v dotazech k vrácení souvisejících částí dat klientovi v jednom dotazu. ID zaměstnance můžete například použít k vyhledání seznamu souvisejících entit, které obsahují údaje o výkonu a kontrole daného zaměstnance.  

Předpokládejme, že ukládáte entity zaměstnanců do Table service pomocí následující struktury:  

![Snímek obrazovky, který ukazuje, jak můžete ukládat entity zaměstnanců v Table service.](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Je také potřeba ukládat historická data týkající se kontrol a výkonu každého roku, který zaměstnanec pracoval ve vaší organizaci, a vy budete mít přístup k těmto informacím po rocích. Jednou z možností je vytvořit další tabulku, která obsahuje entity s následující strukturou:  

![Alternativní struktura entit zaměstnanců](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Všimněte si, že s tímto přístupem se můžete rozhodnout duplikovat některé informace (například křestní jméno a příjmení) v nové entitě, abyste mohli data načíst pomocí jediného požadavku. Nemůžete ale zachovat silnou konzistenci, protože nemůžete použít EGT k tomu, aby se tyto dvě entity provedly atomicky.  

### <a name="solution"></a>Řešení
Pomocí entit s následující strukturou uložte do původní tabulky nový typ entity:  

![Řešení pro strukturu entit zaměstnanců](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Všimněte si, že **RowKey** je teď složený klíč, který se skládá z ID zaměstnance a roku revizních dat, který umožňuje načíst výkon a zkontrolovat data pomocí jediné žádosti pro jednu entitu.  

Následující příklad popisuje, jak můžete načíst všechna data revize pro konkrétního zaměstnance (například zaměstnanec 000123 v prodejním oddělení):  

$filter = (PartitionKey EQ ' Sales ') a (RowKey GE ' empid_000123 ') a (RowKey lt ' 000123_2012 ') &$select = RowKey, hodnocení manažera, rovnocenné hodnocení, komentáře  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Měli byste použít vhodný oddělovací znak, který usnadňuje analýzu hodnoty **RowKey** : například **000123_2012**.  
* Tuto entitu ukládáte i do stejného oddílu jako jiné entity, které obsahují související data pro stejného zaměstnance, což znamená, že můžete pomocí EGTs zachovat silnou konzistenci.
* Měli byste zvážit, jak často budete zadávat dotazy na data, abyste zjistili, jestli je tento model vhodný.  Například pokud budete přistupovat ke kontrole dat nečasto a hlavním datům zaměstnanců, měli byste je často uchovávat jako samostatné entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete uložit jednu nebo více souvisejících entit, které často dotazují.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit  
* [Práce s heterogenními typy entit](#working-with-heterogeneous-entity-types)  
* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Vzor koncového protokolu
Načtěte entity *n* naposledy přidané do oddílu pomocí hodnoty **RowKey** , která se seřadí v pořadí podle data a času.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným požadavkem je, aby bylo možné načíst naposledy vytvořené entity, například 10 nejnovějších deklarací identity odeslaných zaměstnancem. Dotazy tabulky podporují operaci **$Top** dotazu, která vrací první *n* entit ze sady: neexistuje žádná ekvivalentní operace dotazu, která by vrátila Poslední n entit v sadě.  

### <a name="solution"></a>Řešení
Uložte entity pomocí **RowKey** , který přirozeně seřadí v pořadí podle data a času, a to pomocí, takže nejnovější položka je vždy první z nich v tabulce.  

Pokud například chcete mít možnost načíst 10 nejaktuálnějších deklarací výdajů odeslaných zaměstnancem, můžete použít hodnotu reverzního ticku odvozenou od aktuálního data a času. Následující ukázka kódu jazyka C# ukazuje jeden ze způsobů, jak vytvořit vhodnou "obrácenou" hodnotu pro **RowKey** , která bude seřazena od nejnovějšího po nejstarší:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

K hodnotě data a času se můžete vrátit pomocí následujícího kódu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Dotaz na tabulku vypadá takto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zajistit, aby se řetězcová hodnota seřadí podle očekávání, je nutné obklopit hodnotu reverzní Tick počátečními nulami.  
* Musíte znát cíle škálovatelnosti na úrovni oddílu. Buďte opatrní, nevytvářejte oddíly s aktivním bodem.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete získat přístup k entitám v pořadí zpětného data a času nebo potřebujete přístup k naposledy přidaným entitám.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Předřadit/připojit anti-Pattern](#prepend-append-anti-pattern)  
* [Načítání entit](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Vzor vysokého odstranění svazků
Umožňuje odstranit velké množství entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce. Odstraňte entity odstraněním tabulky.  

### <a name="context-and-problem"></a>Kontext a problém
Mnoho aplikací odstraní stará data, která už nejsou k dispozici pro klientskou aplikaci, nebo jestli se aplikace archivuje na jiné paměťové médium. Tato data obvykle identifikujete podle data: například potřebujete odstranit záznamy všech žádostí o přihlášení, které jsou starší než 60 dní.  

Jedním z možných návrhů je použití data a času žádosti o přihlášení v **RowKey**:  

![Datum a čas pokusu o přihlášení](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Tento přístup zabraňuje dělení hotspotů, protože aplikace může vkládat a odstraňovat přihlašovací entity pro každého uživatele v samostatném oddílu. Tento přístup ale může být nákladný a časově náročný, pokud máte velký počet entit, protože nejdřív potřebujete provést prohledávání tabulky, aby bylo možné identifikovat všechny entity, které se mají odstranit, a pak musíte odstranit každou starou entitu. Můžete snížit počet zpátečních cest k serveru nutnému k odstranění starých entit dávkování více žádostí o odstranění do EGTs.  

### <a name="solution"></a>Řešení
Pro každý den pokusů o přihlášení použijte samostatnou tabulku. Můžete použít návrh entity výše, abyste se vyhnuli hotspotům při vkládání entit a odstranili jste staré entity. teď stačí k odstranění jedné tabulky každý den (operace jednoho úložiště) místo vyhledání a odstranění stovek a tisíců jednotlivých přihlašovacích entit každý den.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Podporuje vaše návrh i další způsoby, kterými vaše aplikace bude používat data, jako je například vyhledávání konkrétních entit, propojení s ostatními daty nebo generování agregačních informací?  
* Vyloučí se při vkládání nových entit návrh?  
* Očekává zpoždění, pokud chcete po odstranění znovu použít stejný název tabulky. Je lepší vždycky používat jedinečné názvy tabulek.  
* Očekává se omezení při prvním použití nové tabulky, když Table service zjistí vzory přístupu a distribuuje oddíly mezi uzly. Měli byste zvážit, jak často potřebujete vytvořit nové tabulky.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že máte velké množství entit, které je nutné odstranit ve stejnou dobu.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit
* [Změna entit](#modifying-entities)  

## <a name="data-series-pattern"></a>Vzor datových řad
Uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným scénářem je, že aplikace ukládá řadu dat, která obvykle potřebují k načtení všech najednou. Například vaše aplikace může zaznamenat, kolik zpráv IM každý zaměstnanec odesílá každou hodinu, a pak tyto informace použít k vykreslení počtu zpráv, které každý uživatel poslal za předchozích 24 hodin. Jeden návrh může být ukládat 24 entit pro každého zaměstnance:  

![Ukládat 24 entit pro každého zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

S tímto návrhem můžete snadno vyhledat a aktualizovat entitu, která se má aktualizovat pro každého zaměstnance, kdykoli aplikace potřebuje aktualizovat hodnotu počet zpráv. Chcete-li však načíst informace pro vykreslení grafu aktivity za předchozích 24 hodin, je nutné načíst 24 entit.  

### <a name="solution"></a>Řešení
Pro uložení počtu zpráv pro každou hodinu použijte následující návrh s samostatnou vlastností:  

![Entita statistiky zprávy](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

S tímto návrhem můžete pomocí operace sloučení aktualizovat počet zpráv zaměstnance na určitou hodinu. Nyní můžete načíst všechny informace, které potřebujete k vykreslení grafu, a to pomocí žádosti pro jednu entitu.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Pokud celou datovou řadu nevyhovují jedné entitě (entita může mít až 252 vlastností), použijte alternativní úložiště dat, jako je například objekt BLOB.  
* Pokud máte více klientů s aktualizací entity současně, bude nutné použít **značku ETag** k implementaci optimistického řízení souběžnosti. Pokud máte mnoho klientů, může docházet ke vysokému obsahu.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete aktualizovat a načíst datovou řadu přidruženou k jednotlivé entitě.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor velkých entit](#large-entities-pattern)  
* [Sloučit nebo nahradit](#merge-or-replace)  
* [Vzorec pro nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) (Pokud ukládáte datovou řadu do objektu BLOB)  

## <a name="wide-entities-pattern"></a>Model pro nejrůznější entity
K ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivá entita nemůže mít více než 252 vlastností (kromě povinných vlastností systému) a nemůže uchovávat více než 1 MB dat. V relační databázi byste obvykle zaokrouhlují omezení velikosti řádku přidáním nové tabulky a vynucením vztahu 1:1 mezi nimi.  

### <a name="solution"></a>Řešení
Pomocí Table service můžete uložit více entit, které reprezentují jeden velký obchodní objekt s více než 252 vlastnostmi. Pokud například chcete uložit počet zpráv IM odesílaných jednotlivými zaměstnanci po dobu posledních 365 dní, můžete použít následující návrh, který používá dvě entity s různými schématy:  

![Více entit](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Pokud potřebujete provést změnu, která vyžaduje aktualizaci obou entit, aby byla vzájemně synchronizovaná, můžete použít EGT. V opačném případě můžete použít jednu operaci sloučení k aktualizaci počtu zpráv pro určitý den. Pokud chcete načíst všechna data pro jednotlivé zaměstnance, musíte načíst obě entity, které se dají provádět se dvěma efektivními požadavky, které používají **PartitionKey** a hodnotu **RowKey** .  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Načtení kompletní logické entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení každé fyzické entity.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete ukládat entity, jejichž velikost nebo počet vlastností překračuje limity pro jednotlivé entity v Table service.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* Transakce skupiny entit
* [Sloučit nebo nahradit](#merge-or-replace)

## <a name="large-entities-pattern"></a>Vzor velkých entit
Úložiště objektů blob můžete použít k ukládání velkých hodnot vlastností.  

### <a name="context-and-problem"></a>Kontext a problém
Jednotlivé entity nemůžou ukládat celkem víc než 1 MB dat. Pokud jedna nebo několik vlastností ukládá hodnoty, které způsobí, že celková velikost vaší entity překročí tuto hodnotu, nemůžete uložit celou entitu v Table service.  

### <a name="solution"></a>Řešení
Pokud má vaše entita velikost větší než 1 MB, protože jedna nebo více vlastností obsahuje velké množství dat, můžete ukládat data v Blob service a pak uložit adresu objektu blob do vlastnosti v entitě. Můžete například uložit fotografii zaměstnance v úložišti objektů BLOB a Uložit odkaz na fotografii ve vlastnosti **Photo** vaší entity zaměstnance:  

![Vlastnost Photo](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Chcete-li zachovat konečnou konzistenci mezi entitou v Table service a daty v Blob service, použijte ke správě svých entit [model nakonec konzistentní transakce](#eventually-consistent-transactions-pattern) .
* Načtení kompletní entity zahrnuje alespoň dvě transakce úložiště: jednu pro načtení entity a jednu pro načtení dat objektu BLOB.  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Tento model použijte v případě, že potřebujete uložit entity, jejichž velikost překračuje limity pro jednotlivou entitu v Table service.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor nakonec konzistentních transakcí](#eventually-consistent-transactions-pattern)  
* [Model pro nejrůznější entity](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Předřadit/připojit anti-Pattern
Zvýšení škálovatelnosti, když máte velký objem vložení, rozprostřete vložení do více oddílů.  

### <a name="context-and-problem"></a>Kontext a problém
Nedokončené nebo připojené entity k uloženým entitám obvykle způsobí, že aplikace přidává nové entity do prvního nebo posledního oddílu sekvence oddílů. V tomto případě jsou všechna vložení v daném čase prováděna ve stejném oddílu a vytvoří se hotspot, který brání službě Table z vyrovnávání zatížení na více uzlech, a pravděpodobně způsobí, že aplikace narazí na cíle škálovatelnosti pro oddíl. Například pokud máte aplikaci, která protokoluje přístup k síti a prostředkům zaměstnanci, pak struktura entity, jak je vidět níže, může způsobit, že se oddíl aktuální hodiny stane aktivním, pokud objem transakcí dosáhne cíle škálovatelnosti pro jednotlivý oddíl:  

![Struktura entity](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Řešení
Následující alternativní struktura entity zabraňuje hotspotu na jakémkoli konkrétním oddílu, který je v protokolech událostí aplikace:  

![Alternativní struktura entit](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

V tomto příkladu si všimněte, jak jsou složené klíče **PartitionKey** i **RowKey** . **PartitionKey** používá oddělení i ID zaměstnance k distribuci protokolování napříč více oddíly.  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:  

* Vychází se alternativní klíčovou strukturou, která zabraňuje vytváření aktivních oddílů při vložení, efektivně podporovat dotazy, které klientská aplikace dělá?  
* Znamená to, že jste pravděpodobně dosáhli cílů škálovatelnosti pro jednotlivé oddíly a omezili jste službu úložiště?  

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít
Vyhněte se tomu, aby při přístupu k aktivnímu oddílu nedošlo k omezení objemu transakcí v případě, že dojde ke zpomalení služby úložiště.  

### <a name="related-patterns-and-guidance"></a>Související modely a pokyny
Při implementaci tohoto modelu můžou být relevantní také následující modely a pokyny:  

* [Vzor složeného klíče](#compound-key-pattern)  
* [Vzor koncového protokolu](#log-tail-pattern)  
* [Změna entit](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Anti-Pattern dat protokolu
Obvykle byste místo Table service měli použít Blob service k uložení dat protokolu.  

### <a name="context-and-problem"></a>Kontext a problém
Běžným případem použití pro data protokolu je načtení výběru položek protokolu pro určitý rozsah data a času: například chcete najít všechny chyby a kritické zprávy, které aplikace zaznamenala v rozmezí 15:04 až 15:06 k určitému datu. Nechcete pomocí data a času zprávy protokolu určit oddíl, do kterého ukládáte entity protokolu: to znamená, že v jakémkoli okamžiku všechny entity protokolu budou sdílet stejnou hodnotu **PartitionKey** (viz část [předplatná/Append anti-Pattern](#prepend-append-anti-pattern)). Například následující schéma entity pro zprávu protokolu má za následek aktivní oddíl, protože aplikace zapisuje všechny zprávy protokolu do oddílu aktuálního data a hodiny:  

![Entita zprávy protokolu](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

V tomto příkladu **RowKey** zahrnuje datum a čas zprávy protokolu, aby se zajistilo, že se zprávy protokolu ukládají v pořadí podle data a času, a obsahuje ID zprávy v případě, že více zpráv protokolu sdílí stejné datum a čas.  

Další možností je použít **PartitionKey** , který zajistí, že aplikace zapisuje zprávy do celé řady oddílů. Pokud například zdroj zprávy protokolu poskytuje způsob, jak distribuovat zprávy v mnoha oddílech, můžete použít následující schéma entity:  

![Alternativní entita zprávy protokolu](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problém s tímto schématem je však, že k načtení všech zpráv protokolu v určitém časovém rozsahu musíte vyhledat všechny oddíly v tabulce.

### <a name="solution"></a>Řešení
Předchozí část zdůraznila problém při pokusu o použití Table service k uložení položek protokolu a navržených dvou, neuspokojivých a návrhů. Jedno řešení vedlo k Hot oddílu s rizikem špatného výkonu při zápisu zpráv protokolu; druhé řešení vedlo k nekvalitnímu výkonu dotazů kvůli nutnosti kontrolovat všechny oddíly v tabulce, aby se načetly zprávy protokolu pro určité časové období. Úložiště objektů BLOB nabízí lepší řešení pro tento typ scénáře a jedná se o způsob, jakým Analýza úložiště Azure ukládá data protokolu, která shromažďuje.  

Tato část popisuje, jak Analýza úložiště ukládá data protokolu do úložiště objektů BLOB jako ilustraci tohoto přístupu k ukládání dat, která se obvykle dotazují podle rozsahu.  

Analýza úložiště ukládá zprávy protokolu ve formátu s oddělovači ve více objektech blob. Formát s oddělovači umožňuje klientské aplikaci snadno analyzovat data v protokolu.  

Analýza úložiště používá zásady vytváření názvů pro objekty blob, které umožňují najít objekt BLOB (nebo objekty BLOB), které obsahují zprávy protokolu, pro které hledáte. Například objekt BLOB s názvem Queue/2014/07/31/1800/000001. log obsahuje zprávy protokolu, které se vztahují ke službě Queue po hodinu od 18:00. července 2014. "000001" značí, že se jedná o první soubor protokolu pro toto období. Analýza úložiště také zaznamenává časová razítka první a poslední zprávy protokolu uložené v souboru jako součást metadat objektu BLOB. Rozhraní API pro úložiště objektů BLOB umožňuje vyhledat objekty BLOB v kontejneru na základě předpony názvu: pro vyhledání všech objektů blob, které obsahují data protokolu fronty po dobu od 18:00, můžete použít předponu Queue/2014/07/31/1800.  

Analýza úložiště ukládá zprávy protokolu do vyrovnávací paměti interně a pak pravidelně aktualizuje příslušný objekt BLOB nebo vytvoří nový s nejnovější dávkou položek protokolu. Tím se sníží počet zápisů, které se musí provést ve službě BLOB Service.  

Pokud implementujete podobné řešení ve své vlastní aplikaci, musíte zvážit, jak spravovat kompromisy mezi spolehlivostí (zápis všech protokolů do úložiště objektů BLOB) a náklady a škálovatelnost (aktualizace ve vyrovnávací paměti ve vaší aplikaci a jejich zápis do úložiště objektů BLOB v dávkách).  

### <a name="issues-and-considerations"></a>Problémy a důležité informace
Při rozhodování, jak ukládat data protokolu, vezměte v úvahu následující body:  

* Pokud vytvoříte návrh tabulky, který zabrání potenciálním neaktivním oddílům, můžete zjistit, že k datům protokolu nemůžete efektivně přistupovat.  
* Pro zpracování dat protokolu klient často potřebuje načíst mnoho záznamů.  
* I když jsou data protokolu často strukturovaná, může být lepším řešením úložiště objektů BLOB.  

## <a name="implementation-considerations"></a>Důležité informace o implementaci
Tato část popisuje některé z důležitých informací, které je potřeba mít na paměti při implementaci vzorů popsaných v předchozích částech. Většina tohoto oddílu používá příklady napsané v jazyce C#, které používají knihovnu klienta úložiště (verze 4.3.0 v době psaní).  

## <a name="retrieving-entities"></a>Načítání entit
Jak je popsáno v návrhu oddílu pro dotazování, nejúčinnější dotaz je dotaz typu Point. V některých scénářích ale možná budete potřebovat načíst více entit. Tato část popisuje některé běžné přístupy k načítání entit pomocí klientské knihovny pro úložiště.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Provádění dotazu na bod pomocí klientské knihovny pro úložiště
Nejjednodušší způsob, jak spustit dotaz na bod, je použít operaci **načíst** tabulku, jak je znázorněno v následujícím fragmentu kódu jazyka C#, který načte entitu s **PartitionKey** hodnotou "Sales" a **RowKey** hodnotou "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Všimněte si, jak tento příklad očekává, že entita, kterou načítá, je typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Načítání více entit pomocí LINQ
Pomocí technologie LINQ můžete načíst více entit z Table service při práci s Microsoft Azure standardní knihovnou Cosmos tabulky. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Pokud chcete, aby níže uvedené příklady fungovaly, budete muset zahrnout obory názvů:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

Pole zaměstnanci je objekt v cloudu, který implementuje metodu CreateQuery \<ITableEntity> (), která vrací TableQuery \<ITableEntity> . Objekty tohoto typu implementují rozhraní IQueryable a umožňují použití výrazů dotazů LINQ a syntaxe zápisu teček.

Načítání více entit a jejich dosažení zadáním dotazu s klauzulí **WHERE** . Abyste se vyhnuli prohledávání tabulky, měli byste vždycky do klauzule WHERE zahrnout hodnotu **PartitionKey** a pokud je to možné, **RowKey** hodnotu, abyste se vyhnuli prohledávání tabulek a oddílů. Služba Table Service podporuje omezené sady relačních operátorů (je větší než nebo rovno, menší než, je menší než nebo rovno, rovno a není rovno) pro použití v klauzuli WHERE. 

Následující fragment kódu jazyka C# vyhledá všechny zaměstnance, jejichž příjmení začíná písmenem "B" (za předpokladu, že **RowKey** ukládá příjmení) do prodejního oddělení (za předpokladu, že **PartitionKey** ukládá název oddělení):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Všimněte si, jak dotaz určuje jak **RowKey** , tak **PartitionKey** pro zajištění lepšího výkonu.  

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
> Ukázka vnořování více metod **CombineFilters** , aby zahrnovaly tři podmínky filtru.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Načítání velkého počtu entit z dotazu
Optimální dotaz vrátí jednotlivou entitu na základě hodnoty **PartitionKey** a hodnoty **RowKey** . V některých scénářích ale může být potřeba vrátit mnoho entit ze stejného oddílu nebo dokonce z mnoha oddílů.  

V takových scénářích byste vždy měli plně testovat výkon vaší aplikace.  

Dotaz na službu Table Service může vracet maximálně 1 000 entit najednou a může se provést po dobu maximálně pěti sekund. Pokud sada výsledků obsahuje více než 1 000 entit, pokud dotaz nebyl dokončen do pěti sekund nebo pokud dotaz překračuje hranici oddílu, Table service vrátí token pro pokračování, který umožní klientské aplikaci požádat o další sadu entit. Další informace o tom, jak fungují tokeny pro pokračování, najdete v tématu [časový limit dotazu a stránkování](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Pokud používáte klientskou knihovnu pro úložiště, může automaticky zpracovat tokeny pro pokračování, protože vrací entity z Table service. Následující ukázka kódu C# pomocí klientské knihovny pro úložiště automaticky zpracovává tokeny pokračování, pokud je služba Table Service vrací v odpovědi:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Následující kód jazyka C# zpracovává tokeny pro pokračování explicitně:  

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

Pokud použijete tokeny pro pokračování explicitně, můžete řídit, kdy vaše aplikace načte další segment dat. Například pokud vaše klientská aplikace umožňuje uživatelům stránkovat prostřednictvím entit uložených v tabulce, uživatel se může rozhodnout, že neprojde všemi entitami, které dotaz načetl, aby vaše aplikace použila token pro pokračování k načtení dalšího segmentu, když uživatel dokončil stránkování prostřednictvím všech entit v aktuálním segmentu. Tento přístup má několik výhod:  

* Umožňuje omezit množství dat, která se mají načíst z Table service a která se pohybují po síti.  
* Umožňuje provádět asynchronní vstupně-výstupní operace v .NET.  
* Umožňuje serializovat token pro pokračování na trvalé úložiště, abyste mohli pokračovat v události při selhání aplikace.  

> [!NOTE]
> Token pro pokračování obvykle vrací segment obsahující 1 000 entit, i když může být menší. To platí i v případě, že omezíte počet položek, které dotaz vrátí, pomocí příkazu **přijmout** pro vrácení prvních n entit, které odpovídají kritériím vyhledávání: služba Table Service může vracet segment obsahující méně než n entit spolu s tokenem pokračování, který vám umožní načíst zbývající entity.  
> 
> 

Následující kód jazyka C# ukazuje, jak změnit počet entit vrácených v rámci segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekce na straně serveru
Jedna entita může mít až 255 vlastností a musí mít velikost až 1 MB. Při dotazování tabulky a načtení entit nemusíte potřebovat všechny vlastnosti a můžete se vyhnout nutnosti přenášet data zbytečně (což snižuje latenci a náklady). Pomocí projekce na straně serveru můžete přenést jenom vlastnosti, které potřebujete. Následující příklad načítá pouze vlastnost **email** (společně s **PartitionKey**, **RowKey**, **timestamp** a **ETag**) z entit vybraných dotazem.  

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

Všimněte si, jak je dostupná hodnota **RowKey** , i když nebyla obsažena v seznamu vlastností, které se mají načíst.  

## <a name="modifying-entities"></a>Změna entit
Klientská knihovna pro úložiště umožňuje upravovat entity uložené ve službě Table Service pomocí vkládání, odstraňování a aktualizace entit. EGTs můžete použít ke dávkovému zpracování více operací vložení, aktualizace a odstranění společně ke snížení počtu požadovaných požadavků na zpáteční a ke zvýšení výkonu vašeho řešení.  

Výjimky vyvolané v případě, že klientská knihovna pro úložiště spustí EGT obvykle zahrnuje index entity, která způsobila selhání dávky. To je užitečné, když ladíte kód, který používá EGTs.  

Měli byste také zvážit, jak váš návrh ovlivňuje způsob, jakým vaše klientská aplikace zpracovává operace souběžnosti a aktualizace.  

### <a name="managing-concurrency"></a>Správa souběžnosti
Ve výchozím nastavení služba Table Service implementuje optimistické kontroly souběžnosti na úrovni jednotlivých entit pro operace **vložení**, **sloučení** a **odstranění** , i když je možné, že klient vynutí, aby služba Table Service obcházela tyto kontroly. Další informace o tom, jak služba Table Service spravuje souběžnost, najdete v tématu  [Správa souběžnosti v Microsoft Azure Storage](../blobs/concurrency-manage.md).  

### <a name="merge-or-replace"></a>Sloučit nebo nahradit
Metoda **Replace** třídy **TableOperation** vždy nahradí kompletní entitu v Table Service. Pokud do žádosti v případě, že tato vlastnost existuje v uložené entitě, nezahrnete vlastnost, požadavek tuto vlastnost odebere z uložené entity. Pokud nechcete odebrat vlastnost explicitně z uložené entity, musíte do žádosti zahrnout každou vlastnost.  

Pomocí metody **Merge** třídy **TableOperation** můžete omezit množství dat, která odesíláte do Table Service, když chcete entitu aktualizovat. Metoda **Merge** nahrazuje všechny vlastnosti v uložené entitě hodnotami vlastností z entity obsažené v žádosti, ale ponechá nedotčené vlastnosti v uložené entitě, které nejsou součástí žádosti. To je užitečné, pokud máte velké entity a potřebujete aktualizovat pouze malý počet vlastností v žádosti.  

> [!NOTE]
> Metody **Replace** a **Merge** selžou, pokud entita neexistuje. Alternativně můžete použít metody **InsertOrReplace** a **InsertOrMerge** , které vytvoří novou entitu, pokud neexistuje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Práce s heterogenními typy entit
Table service je úložiště tabulek *bez schématu* , což znamená, že jedna tabulka může ukládat entity více typů, které poskytují skvělou flexibilitu v návrhu. Následující příklad znázorňuje tabulku, která ukládá entity zaměstnanci i oddělení:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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

Každá entita musí mít stále hodnoty **PartitionKey**, **RowKey** a **timestamp** , ale může mít libovolnou sadu vlastností. Kromě toho není nic označovat typ entity, pokud se nerozhodnete ukládat tyto informace někam. Existují dvě možnosti, jak identifikovat typ entity:  

* Předřaďte typ entity do **RowKey** (případně do **PartitionKey**). Například **EMPLOYEE_000123** nebo **DEPARTMENT_SALES** jako hodnoty **RowKey** .  
* Použijte samostatnou vlastnost pro záznam typu entity, jak je znázorněno v následující tabulce.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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

První možnost, předčekání typu entity na **RowKey**, je užitečná, pokud existuje možnost, že dvě entity různých typů mohou mít stejnou klíčovou hodnotu. Také seskupuje entity stejného typu společně v oddílu.  

Techniky popsané v této části jsou obzvláště důležité pro [vztahy dědičnosti](table-storage-design-modeling.md#inheritance-relationships) diskuze výše v tomto průvodci v článku [vztahy modelování](table-storage-design-modeling.md).  

> [!NOTE]
> V hodnotě typ entity byste měli zvážit zahrnutí čísla verze, která umožní klientským aplikacím vyvíjet objekty POCO a pracovat s různými verzemi.  
> 
> 

Zbývající část této části popisuje některé funkce v klientské knihovně pro úložiště, které usnadňují práci s více typy entit ve stejné tabulce.  

### <a name="retrieving-heterogeneous-entity-types"></a>Načítání heterogenních typů entit
Pokud používáte klientskou knihovnu pro úložiště, máte k dispozici tři možnosti pro práci s více typy entit.  

Pokud znáte typ entity uložené s konkrétními hodnotami **RowKey** a **PartitionKey** , můžete zadat typ entity, když načtete entitu, jak je znázorněno v předchozích dvou příkladech, které načítají entity typu **EmployeeEntity**: [provádění dotazu na bod pomocí klientské knihovny pro úložiště](#executing-a-point-query-using-the-storage-client-library) a [načítání více entit pomocí LINQ](#retrieving-multiple-entities-using-linq).  

Druhou možností je použít typ **DynamicTableEntity** (kontejner objektů a dat) místo konkrétního typu entity POCO (Tato možnost může také zvýšit výkon, protože není nutné serializovat a deserializovat entitu na typy rozhraní .NET). Následující kód jazyka C# potenciálně načte více entit různých typů z tabulky, ale vrátí všechny entity jako instance **DynamicTableEntity** . Pak pomocí vlastnosti **EntityType** určí typ každé entity:  

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

Chcete-li načíst další vlastnosti, musíte použít metodu **TryGetValue** ve **vlastnosti Property třídy** **DynamicTableEntity** .  

Třetí možností je kombinovat použití typu **DynamicTableEntity** a instance **EntityResolver** . To umožňuje překládat na více typů POCO ve stejném dotazu. V tomto příkladu delegát **EntityResolver** používá vlastnost **EntityType** k rozlišení mezi dvěma typy entity, které dotaz vrátí. Metoda **Resolve** používá delegáta **překladače** k překladu instancí **DynamicTableEntity** na instance **TableEntity** .  

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

### <a name="modifying-heterogeneous-entity-types"></a>Úpravy heterogenních typů entit
Nemusíte znát typ entity, kterou chcete odstranit, a při vložení vždy znát typ entity. Typ **DynamicTableEntity** však můžete použít k aktualizaci entity bez znalosti jejího typu a bez použití třídy entity POCO. Následující ukázka kódu načte jednu entitu a před aktualizací kontroluje vlastnost **EmployeeCount** .  

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
Tokeny sdíleného přístupového podpisu (SAS) můžete použít, pokud chcete klientským aplikacím povolit úpravy (a dotazování) entit tabulek bez nutnosti zahrnout klíč účtu úložiště do kódu. Použití SAS ve vaší aplikaci má typicky tři hlavní výhody:  

* Nemusíte distribuovat svůj klíč účtu úložiště na nezabezpečenou platformu (například mobilní zařízení), aby toto zařízení mělo přístup k entitám v Table service a jejich úpravy.  
* Můžete přesměrovat určitou práci, kterou webové a pracovní role provádějí při správě entit na klientských zařízeních, jako jsou počítače koncových uživatelů a mobilní zařízení.  
* Klientovi můžete přiřadit omezené a časově omezenou sadu oprávnění (například povolit přístup jen pro čtení ke konkrétním prostředkům).  

Další informace o použití tokenů SAS s Table service najdete v tématu [použití sdílených přístupových podpisů (SAS)](../../storage/common/storage-sas-overview.md).  

Přesto však musíte vygenerovat tokeny SAS, které klientské aplikace udělují entitám ve službě Table Service: měli byste to udělat v prostředí, které má zabezpečený přístup k klíčům účtu úložiště. Obvykle se používá webová role nebo role pracovního procesu pro generování tokenů SAS a jejich doručování klientským aplikacím, které potřebují přístup k vašim entitám. Vzhledem k tomu, že je stále k dispozici režie při generování a poskytování tokenů SAS klientům, byste měli zvážit, jak nejlépe snížit tuto režii, zejména ve scénářích s vysokým objemem.  

Je možné vygenerovat token SAS, který uděluje přístup podmnožině entit v tabulce. Ve výchozím nastavení vytvoříte token SAS pro celou tabulku, ale je také možné určit, že token SAS udělí přístup buď k celé řadě hodnot **PartitionKey** , nebo k rozsahu hodnot **PartitionKey** a **RowKey** . Můžete zvolit generování tokenů SAS pro jednotlivé uživatele systému tak, aby token SAS každého uživatele mohl mít přístup pouze ke svým vlastním entitám ve službě Table Service.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchronní a paralelní operace
Za předpokladu, že rozšíříte žádosti napříč více oddíly, můžete zvýšit propustnost a odezvu klienta pomocí asynchronních nebo paralelních dotazů.
Můžete mít například dvě nebo víc instancí role pracovního procesu, které k vašim tabulkám přistupují paralelně. Můžete mít jednotlivé role pracovních procesů, které jsou zodpovědné za konkrétní sady oddílů, nebo jenom mít několik instancí role pracovního procesu, každý může mít přístup ke všem oddílům v tabulce.  

V rámci instance klienta můžete zvýšit propustnost spuštěním operací úložiště asynchronně. Klientská knihovna pro úložiště usnadňuje psaní asynchronních dotazů a úprav. Můžete například začít s synchronní metodou, která načte všechny entity v oddílu, jak je znázorněno v následujícím kódu jazyka C#:  

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

Tento kód můžete snadno upravit, aby dotaz běžel asynchronně, jak je znázorněno níže:  

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

V tomto asynchronním příkladu vidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje modifikátor **Async** a vrací instanci **úlohy** .  
* Namísto volání metody **ExecuteSegmented** k načtení výsledků metoda nyní volá metodu **ExecuteSegmentedAsync** a použije modifikátor **await** pro asynchronní načtení výsledků.  

Klientská aplikace může zavolat tuto metodu vícekrát (s různými hodnotami pro parametr **oddělení** ) a každý dotaz se spustí v samostatném vlákně.  

V třídě **TableQuery** neexistuje asynchronní verze metody **Execute** , protože rozhraní **IEnumerable** nepodporuje asynchronní výčet.  

Entity můžete také vkládat, aktualizovat a odstraňovat asynchronně. Následující příklad jazyka C# ukazuje jednoduchou, synchronní metodu pro vložení nebo nahrazení entity zaměstnance:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Tento kód můžete snadno upravit tak, aby se aktualizace spouštěla asynchronně, jak je znázorněno níže:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

V tomto asynchronním příkladu vidíte následující změny z synchronní verze:  

* Signatura metody teď obsahuje modifikátor **Async** a vrací instanci **úlohy** .  
* Namísto volání metody **Execute** pro aktualizaci entity nyní metoda volá metodu **metody ExecuteAsync** a používá modifikátor **await** pro asynchronní načítání výsledků.  

Klientská aplikace může volat několik asynchronních metod, jako je tato, a každá metoda vyvolání se spustí v samostatném vlákně.  

## <a name="next-steps"></a>Další kroky

- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)