---
title: Azure platebních zpracování plán, podle kterého - CHD požadavky
description: PCI DSS požadavek 3
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 7ff8a412b16025afe2640b73b51d9b2a2fdca354
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>CHD požadavky pro kompatibilní se standardem PCI DSS prostředí
## <a name="pci-dss-requirement-3"></a>PCI DSS požadavek 3

**Chránit data uložená držitele karty**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Metody ochrany například šifrování, zkrácení, maskování, a použití algoritmu hash jsou důležité součásti ochrany dat držitele karty. Pokud útočníka by se obešla ostatní ovládací prvky zabezpečení a získá přístup k šifrovaným datům bez správné kryptografické klíče, je v datech nečitelná a nepoužitelná této osobě. Jiné efektivní metody ochrany uložených dat by měly být považovány příležitostí ke zmírnění potenciální riziko. Například metody pro minimalizovat riziko patří není ukládání dat držitele karty, pokud data nezbytně nutné, zkracování držitele karty Pokud posunete zobrazení úplné není potřeba a ne odesílání nechráněné zvětšení pomocí zasílání zpráv technologií koncového uživatele, například e-mailu a rychlých zasílání zpráv.
Naleznete PCI DSS a PA-DSS slovníku pojmů, zkratky a zkratky definice "silného šifrování" a dalšími podmínkami PCI DSS.

## <a name="pci-dss-requirement-31"></a>PCI DSS požadavek 3.1

**3.1** úložiště dat zachovat držitele karty na minimum implementací zásad uchovávání a uvolnění dat, postupy a procesy, které patří alespoň pro všechny úložiště dat (CHD) držitele karty:
- Omezení dat úložiště velikost a uchovávání dobu, který je vyžadován pro právní, regulačních a obchodních požadavků
- Požadavky na konkrétní uchování pro držitele karty data
- Procesy pro bezpečné odstraňování dat, když už nepotřebují
- Čtvrtletně proces pro identifikaci a bezpečné odstranění uložených držitele karty data, která překračuje definované uchovávání informací.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure zodpovídá za to, že jsou bezpečně vyřazení pomocí NIST 800-88 kompatibilní protokolů zadaný v jejími zásadami zabezpečení uvolnění data zákazníků, které jsou určené k odstranění. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore neodstraníte ani destroy všechny uložené CHD. Ale všechna data je šifrovaná a žádný z primárních účet číslo (PANORAMOVÁNÍ) data se ukládají.<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS požadavek 3.2

**3.2** neukládejte citlivé ověřovací data po autorizaci (i v případě, že šifrované). Pokud není ověřování citlivých dat, vykreslení všechna data neopravitelné po dokončení procesu autorizace. 
- Je obchodního oprávnění, a 
- Data jsou bezpečně uloženy.
Data citlivé ověřování zahrnuje data, jak je uvedené v následující požadavky 3.2.1 prostřednictvím 3.2.3:


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore odstranit nebo zničit všechny uložené CHD; Ukázková data ukládána po pouze pro účely ukázky. Šifrované a ne primární účet, číslo (PANORAMOVÁNÍ) data uložená se ale všechna data.<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS požadavek 3.2.1

**3.2.1** neukládejte úplný obsahu jakékoli sledování (z magnetické stripe nachází na zadní straně karty, ekvivalentní data obsažená na čip TPM, nebo jinde) po autorizaci. Tato data můžete taky nazývá úplné sledování, sledování, sledovat 1, sledovat, 2 a data magnetické stripe. 

> [!NOTE]
> Následující prvky dat z magnetické stripe běžné obchodní činnosti, může třeba pro zachování: 
> - Vlastník karty 
> - Číslo primární účtu (PANORAMOVÁNÍ) 
> - Datum konce platnosti 
> - Kódu služby 
>
> Chcete-li minimalizovat riziko, uložte jenom tyto datové prvky podle potřeby pro firmy.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore nejsou uložené žádné CHD celý obsah.|



### <a name="pci-dss-requirement-322"></a>PCI DSS požadavek 3.2.2

**3.2.2** po autorizaci neukládejte karty ověřovací kód nebo hodnotu (třímístné nebo čtyřmístné číslo vytištěné na přední nebo zadní platební karta používá k ověření transakce karty. není přítomen).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore šifruje všechna data, včetně CVV ukázky.|



### <a name="pci-dss-requirement-323"></a>PCI DSS požadavek 3.2.3

**3.2.3** neukládejte osobní identifikační číslo (PIN) nebo šifrované blok kódu PIN po autorizaci.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore neumožňuje ukládání informací o PIN kódu.|



## <a name="pci-dss-requirement-33"></a>PCI DSS požadavek 3.3

**3.3** maska POSOUVÁNÍ při zobrazení (první šesti a poslední čtyři číslice jsou maximální počet číslic, který se má zobrazit), tak, aby pouze zaměstnanci legitimní obchodní potřebovat můžete zobrazit úplnou Bluetooth 

> [!NOTE]
> Tento požadavek nenahrazuje přísnější požadavky na místě pro zobrazí data držitele karty – například právní nebo platební karty brand požadavky pro POS potvrzení (POS).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore zakrývá číslo primární účet (PANORAMOVÁNÍ) pomocí transparentní šifrování dat, vždy šifrované sloupce a maskování dynamická data. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>PCI DSS požadavek 3.4

**3.4** vykreslení PAN nečitelná odkudkoli (včetně na přenosné digitálního média, zálohovacího média a v protokolech) je uložen pomocí některé z následujících postupů:
- Jednosměrné hodnoty hash podle silné šifrování (hodnota hash musí být celé PANORAMOVÁNÍ)
- Zkrácení (algoritmu hash nelze použít k nahrazení zkrácený segment PANORAMOVÁNÍ)
- Index tokeny a dotyková zařízení (dotyková zařízení musí být bezpečně uložen)
- Silné šifrování se přidružených procesů správy klíčů a postupy. 

> [!NOTE]
> Je poměrně trivial úsilí pro uživatelů se zlými úmysly k rekonstrukci původní PAN dat v případě, že mají přístup k zkrácený a hash verzi Bluetooth Kde je použita hodnota hash a zkrácen verzích stejné PAN se nacházejí v prostředí entity, další ovládací prvky musí být k zajištění, že verze hash a zkrácený nelze časově rekonstrukci původní Bluetooth

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore šifruje všechna data platební karty a používat Azure Key Vault pro správu klíčů, brání načtení CHD.<br /><br />Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>PCI DSS požadavek 3.4.1

**3.4.1** při použití šifrování disku (místo šifrování databáze úrovni souboru nebo sloupec), se musí spravovat logického přístupu samostatně a nezávisle na ověření nativní operačního systému a mechanismy řízení přístupu (například tím, že není pomocí místního uživatelského účtu databáze nebo obecné síťové přihlašovací údaje). Dešifrovací klíče nesmí být přidruženy uživatelské účty. 

> [!NOTE]
> Tento požadavek platí kromě všech ostatních PCI DSS šifrování a požadavky na správu klíčů.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore všechny uložené data jsou zašifrována a odděluje přenosy, aby privilegované zvýšení oprávnění pro funkce DevOps.<br /><br />Služba App Service Environment je zabezpečená a uzamčené, je potřeba povolit pro všechny verze DevOps nebo změny, které může být nezbytné, jako je například možnost monitorování webové aplikace pomocí modulu Kudu mechanismus.<br /><br />Virtuální počítač je vytvořeno jako jumpbox (bastionu hostitel) s následující konfigurace:<br /><br /><ul><li>[Rozšíření proti malwaru](/azure/security/azure-security-antimalware)</li><li>[Rozšíření monitorování analýzy protokolů](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Rozšíření diagnostiky virtuálního počítače](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Nástroj BitLocker šifrované disku](/azure/security/azure-security-disk-encryption)</li></ul>Použití Azure Key Vault zarovnaná s Azure Government, PCI DSS a HIPAA požadavky.|



## <a name="pci-dss-requirement-35"></a>PCI DSS požadavek 3.5

**3.5** dokumentu a implementujte postupy k ochraně klíče, které slouží k zabezpečení dat uložených držitele karty proti úniku a zneužití. 

> [!NOTE]
> Tento požadavek platí pro klíče, které slouží k šifrování dat uložených držitele karty a platí také pro klíč šifrování klíče, které slouží k ochraně dat šifrování klíče – tyto klíče šifrování klíče musí být silné jako klíč dat šifrování.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Microsoft Azure zajistí, že zákazník trezorů klíčů jsou logicky izolované od sebe navzájem a logicky izolované od roviny správy do služby Key Vault. Key Vault je navržený tak, aby Microsoft nemá žádné stálý přístup k trezoru klíčů zákazníka. <br /><br />Klíče jsou chráněné službou Microsoft Azure, pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM).<br /><br />Klíče uloženého v Microsoft Azure Key Vault může použít k ochraně jiný klíč. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje dokumentaci pro ilustraci a pomáhají nasazení chráněných klíčů řešení ochrany ukázku CHD.|



### <a name="pci-dss-requirement-351"></a>PCI DSS požadavek 3.5.1

**3.5.1** *další požadavek pro poskytovatele služeb pouze:* udržovat zdokumentovaných popis kryptografických architekturu, která zahrnuje:
- Podrobnosti o všech algoritmy, protokoly a klíče, které slouží k ochraně dat držitele karty, včetně klíče sílu a poslední den
- Popis použití klíče pro každý klíč
- Inventář všechny moduly hardwarového zabezpečení a jiné SCDs použít pro správu klíčů 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Microsoft Azure zajistí, že zákazník trezorů klíčů jsou logicky izolované od sebe navzájem a logicky izolované od roviny správy do služby Key Vault. Key Vault je navržený tak, aby Microsoft nemá žádné stálý přístup k trezoru klíčů zákazníka. <br /><br />Klíče jsou chráněné službou Microsoft Azure, pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM).<br /><br />Klíče uloženého v Microsoft Azure Key Vault může použít k ochraně jiný klíč. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje dokumentaci pro ilustraci a pomáhají nasazení chráněných klíčů řešení ochrany ukázku CHD.|



### <a name="pci-dss-requirement-352"></a>PCI DSS požadavek 3.5.2

**3.5.2** omezit přístup k kryptografické klíče nejmenšího počtu správce klíče nezbytné.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Key Vault podporuje zásady granulární přístupu, které umožňují vlastníka Key Vault pro udělení přístupu ke konkrétním funkcím provádět určité operace na konkrétní entity. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Správa klíčů společnosti Contoso Webstore je izolovaný na jeden uživatelský účet (správce ##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>PCI DSS požadavek 3.5.3

**3.5.3** ukládat tajné a soukromé klíče, které se používá k šifrování a dešifrování dat držitele karty v jednom (nebo více) z následujících podob za všech okolností:
- Šifrován klíčem šifrování klíče, je přinejmenším stejná jako klíč šifrování dat a který je uložený samostatně z klíče šifrování dat
- V rámci zabezpečené kryptografické zařízení (například modulu (hostitel) hardwarového zabezpečení (HSM) nebo schválení b. bodu interakce zařízení)
- Jako alespoň dvě součásti i délku klíče nebo klíče sdílené složky v souladu odvětví - přijato – metoda 

> [!NOTE]
> Není nutné, aby veřejné klíče uložené v jednom z těchto formulářů.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Klíče jsou uloženy v konkrétních trezorů klíčů identifikovaný zákazníka.<br /><br />Key Vault je přístupný současně a globálně více aplikací, což snižuje nutnost zkopírovat klíč a uložit na víc umístění. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>PCI DSS požadavek 3.5.4

**3.5.4** uložení šifrovacích klíčů v nejmenší počet možných umístěních.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Klíče jsou uloženy v konkrétních trezorů klíčů identifikovaný zákazníka. <br /><br />Key Vault je přístupný současně a globálně více aplikací, což snižuje nutnost zkopírovat klíč a uložit na víc umístění. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>PCI DSS požadavek 3.6

**3.6** plně dokumentu a implementaci všech procesů správy klíčů a postupy pro kryptografické klíče pro šifrování dat držitele karty, včetně následujících. 

> [!NOTE]
> Řada oborových standardů pro správu klíčů jsou dostupné z různých zdrojů včetně NIST najdete na http://csrc.nist.gov.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>PCI DSS požadavek 3.6.1

**3.6.1** generování silné kryptografických klíčů

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:** <br /><br />Při generování klíčů v Key Vault, Azure je zodpovědný za generování klíčů za specifikace zákazníka. Klíče se generují pomocí modul HSM. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>PCI DSS požadavek 3.6.2

**3.6.2** zabezpečení distribuce

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Přineste si vlastní klíč (BYOK) nástroj zapouzdří klíče zákazníků a cílem konkrétní bezpečnostní trezoru, který je vázaný na konkrétní předplatné Azure. Klíč se dají importovat jenom do trezoru klíčů definované předplatného v zadané oblasti. Tento proces používá šifrování postupy od výrobce hardwaru. Zákazníci obdrží oznámení, že přenos bylo úspěšné. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>PCI DSS požadavek 3.6.3

**3.6.3** zabezpečené kryptografické klíče úložiště

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Klíče jsou uložené v moduly hardwarového zabezpečení a jsou zabezpečené pomocí kryptografických zabezpečení od výrobce hardwaru. Metadata na klíče je uložená ve službě Azure Storage v šifrovaného stavu, který je jedinečný pro každý trezor klíčů. <br /><br /> |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>PCI DSS požadavek 3.6.4

**3.6.4** kryptografické klíče změny pro klíče, které mají bylo dosaženo konce jejich cryptoperiod (například po uplynutí určeného období nebo po určité množství šifrovaný text byl získán k danému klíči), jak je definované s přiřazenou třídou dodavatele aplikace nebo klíče vlastníka a na základě oborových doporučené postupy a pokyny (například speciální publikace NIST 800-57).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Key Vault podporuje funkce aktualizace nebo odebrání klíče, který je definován zákazník. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>PCI DSS požadavek 3.6.5

**3.6.5** vyřazení nebo nahrazení (například archivace, odstraňování nebo odvolání) klíče, které jsou nezbytné, pokud má byla oslabí integritu klíč (například odeslání zaměstnanec s znalostmi klíč prostý text součást), nebo klíče jsou podezření, že bude ohrožen. 

> [!NOTE]
> Pokud vyřadíte, případně vyměníte nutné kryptografické klíče pro zachování, musí tyto klíče bezpečně archivovat (například pomocí klíče šifrovací klíč). Archivovaných kryptografických klíčů by měl použít pouze pro účely dešifrování nebo ověření.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Key Vault podporuje funkce vyřadit z provozu nebo nahrazení, který je definován zákazník. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>PCI DSS požadavek 3.6.6

**3.6.6** Pokud ruční prostého textu kryptografických operací správy klíčů se používají, tyto operace se musí spravovat pomocí znalostní báze rozdělení a duální řízení. 

> [!NOTE]
> Příklady ruční operací správy klíčů zahrnovat, ale nejsou omezeny: klíč generace, přenos, načtení, úložiště a odstraňování.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>PCI DSS požadavek 3.6.7

**3.6.7** bránění neoprávněným nahrazení kryptografické klíče.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Pro zákazníky používající Key Vault:**<br /><br />Trezory klíč jsou logicky oddělené a nepodporují ověřování mezi adresáři. V důsledku toho je zabránit neoprávněným nahrazení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>PCI DSS požadavek 3.6.8

**3.6.8** požadavek pro kryptografické klíče správce klíče oficiálně potvrdit, pochopit a přijmout jejich zodpovědnosti klíč správce.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Správa klíčů společnosti Contoso Webstore je izolovaný na jeden uživatelský účet (správce ##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>PCI DSS požadavek 3.7

**3.7** zajistěte, aby zásady zabezpečení a provozních postupů pro ochranu dat uložených držitele karty jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Key Vault pro všechny správu klíčů. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|




