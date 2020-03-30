---
title: Zpráva k vydání verze Průzkumníka služby Microsoft Azure Storage
description: Poznámky k vydání pro Průzkumníka úložiště Microsoft Azure
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351056"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Zpráva k vydání verze Průzkumníka služby Microsoft Azure Storage

Tento článek obsahuje nejnovější poznámky k verzi pro Azure Storage Explorer, stejně jako poznámky k verzi pro předchozí verze. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která vám umožní snadno pracovat s daty Azure Storage na Windows, macOS a Linuxu.

Chcete-li stáhnout předchozí verze Průzkumníka úložiště, můžete navštívit [stránku Vydání](https://github.com/microsoft/AzureStorageExplorer/releases) našeho úložiště GitHub.

## <a name="version-1110"></a>Verze 1.11.0
11/4/2019

### <a name="new"></a>Nová
* Operace pro objekty BLOB, ADLS Gen2 a spravované disky používají integrovanou AzCopy. Přesněji řečeno, následující operace se provádějí pomocí AzCopy:
   * Objekty blob
      * Otevřeno pro úpravy + nahrávání
      * Nahrávání, včetně přetažení & přetažení
      * Stáhnout
      * Kopírovat & vložit #1249
      * Odstranění
   * ADLS Gen2 Objekty blob
      * Nahrávání, včetně přetažení & přetažení
      * Stáhnout
      * Kopírovat & vložení
      * Odstranit, včetně odstranění složky
   * Spravované disky
      * Odeslat
      * Stáhnout
      * Kopírovat & vložení

   Kromě toho bylo do integrovaného prostředí AzCopy přidáno několik často požadovaných funkcí:
   * Řešení konfliktů – během převodů budete vyzváni k vyřešení konfliktů. #1455
   * Nahrát jako stránky blobs - můžete si vybrat, zda AzCopy nahraje .vhd a .vhdx soubory jako stránky blobs. #1164 a #1601
   * Konfigurovatelné parametry AzCopy - Bylo přidáno několik nastavení pro vyladění výkonu a využití prostředků AzCopy. Další podrobnosti naleznete níže.

* Chcete-li povolit přístup k více protokolům ADLS Gen2 a objektů blobs a dále vylepšit prostředí ADLS Gen2, přidali jsme pro účty ADLS Gen2 následující funkce:
   * Hledání pomocí popisných názvů pro nastavení oprávnění seznamu ACL
   * Zobrazení skrytých kontejnerů, například $logs a $web
   * Získat a přerušit zapůjčení kontejneru
   * Získat a přerušit #848 zapůjčení objektů Blob
   * Správa zásad přístupu ke kontejnerům
   * Konfigurace úrovní přístupu k objektům Blob
   * Kopírovat & vložit objekty BLOB

* V této verzi jsme náhled 17 dalších jazyků. Můžete přepnout do jazyka dle vašeho výběru na stránce nastavení pod "Aplikace" → "Regionální nastavení" → "Jazyk (Náhled)". Stále usilovně pracujeme na překladu dalších řetězců a zlepšení kvality překladu. Pokud máte nějakou zpětnou vazbu ohledně překladu, nebo pokud si všimnete řetězce, který ještě není přeložen, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* V každé verzi se snažíme napalubě několik nastavení, aby jemné soustružení Storage Explorer. V této verzi jsme přidali nastavení pro další konfiguraci AzCopy a také pro skrytí uzlů služby:
   * Limit šířky pásma AzCopy - pomáhá řídit, kolik sítě AzCopy používá. Toto nastavení najdete na "Transfers" → "AzCopy" → "Maximální přenosová rychlost". #1099
   * AzCopy MD5 check - umožňuje nakonfigurovat, zda a jak přísně AzCopy kontroluje hash MD5 při stahování. Toto nastavení najdete na "Transfers" → "AzCopy" → "Check MD5".
   * AzCopy souběžnost a velikost vyrovnávací paměti paměti - ve výchozím nastavení AzCopy bude analyzovat váš počítač určit přiměřené výchozí hodnoty pro tato nastavení. Pokud však narazíte na problémy s výkonem, tato pokročilá nastavení lze dále přizpůsobit, jak azCopy běží v počítači. Tato nastavení naleznete v části "Převody" → "AzCopy". #994
   * Zobrazit a skrýt uzly služby – tato nastavení vám poskytují možnosti zobrazit nebo skrýt některou ze služeb Azure, které Podporuje Průzkumník a úložiště. Tato nastavení naleznete v části "Služby". #1877

* Při vytváření snímku spravovaného disku je nyní k dispozici výchozí název. #1847
* Při připojování s Azure AD, pokud připojíte kontejner objektů blob ADLS Gen2, pak "(ADLS Gen2)" se zobrazí vedle uzlu. #1861

### <a name="fixes"></a>Opravy
* Při kopírování, nahrávání nebo stahování velkých disků by Průzkumník úložiště někdy neodvolal přístup k diskům zapojeným do operace. To jsme opravili. #2048
* Statistika tabulky se nezdařila při zobrazení dotazu klíče oddílu. To jsme opravili. #1886

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště 1.11.0 nyní vyžaduje koncový bod DFS (například "myaccount.dfs.core.windows.net") připojit k kontejnerům ADLS Gen2. Předchozí verze Průzkumníka úložiště vám umožnily používat koncový bod objektu blob. Tyto přílohy již nemusí fungovat po upgradu na 1.11.0. Pokud narazíte na tento problém, znovu připojte pomocí koncového bodu dfs.
* Číselná nastavení nejsou kontrolována, zda leží v platném rozsahu.#2140
* Kopírování kontejnerů objektů blob z jednoho účtu úložiště do jiného ve stromovém zobrazení může selhat. Tento problém vyšetřujeme.#2124
* Nastavení Automatické aktualizace ještě nemá vliv na všechny operace v Průzkumníku objektů blob.
* Funkce spravovaného disku nejsou v Azure Stacku podporované.
* Pokud se nahrávání nebo vkládání disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník úložiště disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné ponechat nový disk v poškozeném stavu. Pokud k tomu dojde, je třeba odstranit nový disk nebo ručně zavolat disková api, abyste nahradili obsah disku tak, aby již nebyl poškozen.
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)

## <a name="previous-releases"></a>Předchozí verze

* [Verze 1.10.1](#version-1101)
* [Verze 1.10.0](#version-1100)
* [Verze 1.9.0](#version-190)
* [Verze 1.8.1](#version-181)
* [Verze 1.8.0](#version-180)
* [Verze 1.7.0](#version-170)
* [Verze 1.6.2](#version-162)
* [Verze 1.6.1](#version-161)
* [Verze 1.6.0](#version-160)
* [Verze 1.5.0](#version-150)
* [Verze 1.4.4](#version-144)
* [Verze 1.4.3](#version-143)
* [Verze 1.4.2](#version-142)
* [Verze 1.4.1](#version-141)
* [Verze 1.3.0](#version-130)
* [Verze 1.2.0](#version-120)
* [Verze 1.1.0](#version-110)
* [Verze 1.0.0](#version-100)
* [Verze 0.9.6](#version-096)
* [Verze 0.9.5](#version-095)
* [Verze 0.9.4 a 0.9.3](#version-094-and-093)
* [Verze 0.9.2](#version-092)
* [Verze 0.9.1 a 0.9.0](#version-091-and-090)
* [Verze 0.8.16](#version-0816)
* [Verze 0.8.14](#version-0814)
* [Verze 0.8.13](#version-0813)
* [Verze 0.8.12 a 0.8.11 a 0.8.10](#version-0812-and-0811-and-0810)
* [Verze 0.8.9 a 0.8.8](#version-089-and-088)
* [Verze 0.8.7](#version-087)
* [Verze 0.8.6](#version-086)
* [Verze 0.8.5](#version-085)
* [Verze 0.8.4](#version-084)
* [Verze 0.8.3](#version-083)
* [Verze 0.8.2](#version-082)
* [Verze 0.8.0](#version-080)
* [Verze 0.7.20160509.0](#version-07201605090)
* [Verze 0.7.20160325.0](#version-07201603250)
* [Verze 0.7.20160129.1](#version-07201601291)
* [Verze 0.7.20160105.0](#version-07201601050)
* [Verze 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Verze 1.10.1
9/19/2019

### <a name="hotfix"></a>Opravy hotfix
* Někteří uživatelé zjistili chybu v 1.10.0 při pokusu o zobrazení jejich dat v jejich účty ADLS Gen 1. Tato chyba zabránila správnému vykreslování panelu průzkumníka. To jsme opravili. #1853 #1865

### <a name="new"></a>Nová
* Průzkumník úložiště má nyní vyhrazené nastavení. Můžete k němu přistupovat buď z Upravit → Nastavení, nebo kliknutím na ikonu Nastavení (ozubené kolo) v levém svislém panelu nástrojů. Tato funkce je prvním krokem, který podnikáme směrem k poskytování různých [uživatelských požadovaných nastavení](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Počínaje touto verzí jsou podporována následující nastavení:
  * Motiv
  * Proxy server
  * Odhlášení při ukončení #6
  * Povolení přihlášení k toku kódu zařízení
  * Automatické obnovení #1526
  * Povolit AzCopy
  * AzCopy SAS doba trvání Pokud existují další nastavení, které chcete vidět přidáno, [otevřete problém na GitHubpopisující](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) nastavení, které chcete vidět.
* Průzkumník úložiště nyní podporuje spravované disky. Můžete:
  * Nahrání místního virtuálního pevného disku na nový disk
  * Stažení disku
  * Kopírování a vkládání disků mezi skupinami prostředků a oblastmi
  * Odstranit disky
  * Vytvoření snímku disku

Nahrávání, stahování a kopírování disků mezi oblastmi je napájeno azCopy v10.
* Průzkumník storage lze nyní nainstalovat prostřednictvím úložiště Snap na Linuxu. Při instalaci prostřednictvím úložiště Snap jsou nainstalovány všechny závislosti za vás, včetně .NET Core! V současné době jsme ověřili, že Storage Explorer běží dobře na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z úložiště Snap na jiných distribucích Linuxu, [otevřete problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu Snap najdete v našem [průvodci začínáme](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Byly provedeny dvě hlavní změny, které se mají připojit pomocí služby Azure Active Directory (Azure AD), které mají tuto funkci učinit užitečnější pro uživatele ADLS Gen2:
  * Nyní vyberete klienta, ve které se přikládá prostředek. To znamená, že již nemusíte mít přístup RBAC k předplatnému prostředku.
  * Pokud připojujete kontejner objektů blob ADLS Gen2, můžete nyní připojit k určité cestě v kontejneru.
* Při správě seznamů ACL pro soubory a složky ADLS Gen2 nyní Průzkumník úložiště zobrazí popisné názvy entit v seznamu ACL. #957
* Při přidávání přes OID do ADLS Gen2 ACL, Průzkumník úložiště nyní ověří, že OID patří do platné entity ve vašem tenantovi. #1603
* Klávesové zkratky pro navigaci mezi kartami nyní používají více standardních kombinací kláves. #1018
* Prostředním kliknutím na kartě ji nyní zavřete. #1348
* Pokud přenos AzCopy obsahuje přeskočení a žádné chyby, Průzkumník úložiště nyní zobrazí ikonu upozornění, která zvýrazní, že došlo k přeskočení. #1490
* Integrovaná AzCopy byla aktualizována na verzi 10.2.1. Kromě toho můžete nyní zobrazit verzi AzCopy nainstalovanou v dialogovém okně Informace. #1343

### <a name="fixes"></a>Opravy
* Mnoho uživatelů má spustit do různých "nelze číst verzi undefined" nebo "nelze číst připojení nedefinovaných" chyb při práci s připojenými účty úložiště. Přestože stále pokračujeme ve zkoumání hlavní příčiny tohoto problému, v 1.10.0 jsme vylepšili zpracování chyb kolem načítání připojených účtů úložiště. #1626, #985 a #1532
* Strom průzkumníka (na levé straně) se mohl dostat do stavu, kdy by fokus opakovaně přeskočil na horní uzel. To jsme opravili. #1596
* Při správě snímků objektu blob by programy pro čtení z obrazovky nečetly časové razítko přidružené ke snímku. To jsme opravili. #1202
* Nastavení proxy serveru v systému macOS nebylo nastaveno včas, aby je proces ověřování mohl používat. To jsme opravili. #1567
* Pokud účet úložiště v suverénním cloudu byl připojen pomocí názvu a klíče, AzCopy nebude fungovat. To jsme opravili. #1544
* Při připojování pomocí připojovacího řetězce průzkumník a úložiště nyní odebere koncové mezery. #1387

### <a name="known-issues"></a>Známé problémy
* Nastavení Automatické aktualizace ještě nemá vliv na všechny operace v Průzkumníku objektů blob.
* Funkce spravovaného disku nejsou v Azure Stacku podporované.
* Pokud se nahrávání nebo vkládání disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník úložiště disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné ponechat nový disk v poškozeném stavu. Pokud k tomu dojde, je třeba odstranit nový disk nebo ručně zavolat disková api, abyste nahradili obsah disku tak, aby již nebyl poškozen.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné ponechat nový disk v poškozeném stavu. Pokud k tomu dojde, je třeba odstranit nový disk nebo ručně zavolat disková api, abyste nahradili obsah disku tak, aby již nebyl poškozen.
* Při provádění stahování blob bez AzCopy není ověřeno MD5 pro velké soubory. Důvodem je chyba v sada SDK úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)


## <a name="version-1100"></a>Verze 1.10.0
9/12/2019

### <a name="new"></a>Nová

* Průzkumník úložiště má nyní vyhrazené nastavení. Můžete k němu přistupovat buď z Upravit → Nastavení, nebo kliknutím na ikonu Nastavení (ozubené kolo) v levém svislém panelu nástrojů. Tato funkce je prvním krokem, který podnikáme směrem k poskytování různých [uživatelských požadovaných nastavení](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Počínaje touto verzí jsou podporována následující nastavení:
    * Motiv
    * Proxy server
    * Odhlášení při ukončení [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Povolení přihlášení k toku kódu zařízení
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) automatické aktualizace
    * Povolit AzCopy
    * Doba trvání AzCopy SAS

    Pokud existují další nastavení, která chcete vidět přidána, [otevřete prosím problém na GitHubu popisující nastavení, které chcete vidět](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Průzkumník úložiště nyní podporuje spravované disky. Můžete:
    * Nahrání místního virtuálního pevného disku na nový disk
    * Stažení disku
    * Kopírování a vkládání disků mezi skupinami prostředků a oblastmi
    * Odstranit disky
    * Vytvoření snímku disku

    Nahrávání, stahování a kopírování disků mezi oblastmi je napájeno azCopy v10.
* Průzkumník storage lze nyní nainstalovat prostřednictvím úložiště Snap na Linuxu. Při instalaci prostřednictvím úložiště Snap jsou nainstalovány všechny závislosti za vás, včetně .NET Core! V současné době jsme ověřili, že Storage Explorer běží dobře na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z úložiště Snap na jiných distribucích Linuxu, [otevřete problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu Snap najdete v našem [průvodci začínáme](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Byly provedeny dvě hlavní změny připojit s Azure Active Directory (Azure AD), které jsou určeny k tomu, aby funkce užitečnější pro uživatele ADLS Gen2: * Nyní vyberte klienta, který prostředek, který připojujete je. To znamená, že již nemusíte mít přístup RBAC k předplatnému prostředku.
        * Pokud připojujete kontejner objektů blob ADLS Gen2, můžete nyní připojit k určité cestě v kontejneru.
* Při správě seznamů ACL pro soubory a složky ADLS Gen2 nyní Průzkumník úložiště zobrazí popisné názvy entit v seznamu ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Při přidávání přes OID do ADLS Gen2 ACL, Průzkumník úložiště nyní ověří, že OID patří do platné entity ve vašem tenantovi. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Klávesové zkratky pro navigaci mezi kartami nyní používají více standardních kombinací kláves. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Prostředním kliknutím na kartě ji nyní zavřete. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Pokud přenos AzCopy obsahuje přeskočení a žádné chyby, Průzkumník úložiště nyní zobrazí ikonu upozornění, která zvýrazní, že došlo k přeskočení. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Integrovaná AzCopy byla aktualizována na verzi 10.2.1. Kromě toho můžete nyní zobrazit verzi AzCopy nainstalovanou v dialogovém okně Informace. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Opravy

* Mnoho uživatelů má spustit do různých "nelze číst verzi undefined" nebo "nelze číst připojení nedefinovaných" chyb při práci s připojenými účty úložiště. Přestože stále pokračujeme ve zkoumání hlavní příčiny tohoto problému, v 1.10.0 jsme vylepšili zpracování chyb kolem načítání připojených účtů úložiště. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)a [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Strom průzkumníka (na levé straně) se mohl dostat do stavu, kdy by fokus opakovaně přeskočil na horní uzel. To jsme opravili. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Při správě snímků objektu blob by programy pro čtení z obrazovky nečetly časové razítko přidružené ke snímku. To jsme opravili. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Nastavení proxy serveru v systému macOS nebylo nastaveno včas, aby je proces ověřování mohl používat. To jsme opravili. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Pokud účet úložiště v suverénním cloudu byl připojen pomocí názvu a klíče, AzCopy nebude fungovat. To jsme opravili. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Při připojování pomocí připojovacího řetězce průzkumník a úložiště nyní odebere koncové mezery. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Známé problémy

* Nastavení Automatické aktualizace ještě nemá vliv na všechny operace v Průzkumníku objektů blob.
* Funkce spravovaného disku nejsou v Azure Stacku podporované.
* Pokud se nahrávání nebo vkládání disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník úložiště disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné ponechat nový disk v poškozeném stavu. Pokud k tomu dojde, je třeba odstranit nový disk nebo ručně zavolat disková api, abyste nahradili obsah disku tak, aby již nebyl poškozen.
* Při provádění stahování blob bez AzCopy není ověřeno MD5 pro velké soubory. Důvodem je chyba v sada SDK úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)

## <a name="version-190"></a>Verze 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Stažení Průzkumníka úložiště Azure 1.9.0
- [Azure Storage Explorer 1.9.0 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Teď můžete připojit kontejnery objektů blob prostřednictvím Azure AD (Oprávnění RBAC nebo ACL). Tato funkce je určena k pomoci uživatelům, kteří mají přístup k kontejnerům, ale ne účty úložiště, které kontejnery jsou v. Další informace o této funkci najdete v příručce Začínáme.
* Získat a přerušit zapůjčení nyní pracovat s RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Správa zásad přístupu a nastavení úrovně veřejného přístupu nyní pracují s RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Odstranění složek objektů blob nyní funguje s RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Změna úrovně přístupu k objektům blob teď funguje s RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Nyní můžete rychle obnovit rychlý přístup pomocí "Nápovědy" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funkce Preview

* Přihlášení ke toku kódu zařízení je nyní k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použít přihlášení k kódu zařízení". Doporučujeme všem uživatelům, kteří měli problémy s prázdnými přihlašovacími okny, aby tuto funkci vyzkoušeli, protože se může ukázat jako spolehlivější forma přihlášení.
* Průzkumník úložiště integrovaný s AzCopy je v současné době k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použijte AzCopy pro lepší nahrávání a stahování objektů Blob". Převody objektů BLOB dokončené azCopy by měly být rychlejší a výkonnější.

### <a name="fixes"></a>Opravy

* Opraveno, že se nepodařilo načíst více než 50 předplatných pro jeden účet. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Opraveno tlačítko "Přihlásit se" nefunguje na informačním panelu, který se zobrazí, když selže přímý odkaz. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Opraveno nahrávání souborů .app na macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Opraveno "Opakovat vše" nefunguje pro neúspěšný přejmenování objektu blob. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Opraveno "Zrušit" nefunguje při otevírání objektu blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Opraveny problémy s pravopisem a popisky v celém produktu. Děkujeme všem, kteří tyto problémy nahlásili! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395) , [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336) [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368 #1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování blob bez AzCopy není ověřeno MD5 pro velké soubory. Důvodem je chyba v sada SDK úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Při pokusu o přístup k objektům BLOB ADLS Gen2 za proxy může selhat.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)

## <a name="version-181"></a>Verze 1.8.1
5/13/2019

### <a name="hotfixes"></a>Opravy hotfix
* V některých případech by kliknutím na tlačítko "Načíst více" na úrovni prostředků nevrátil další stránku prostředků. To jsme opravili. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* V systému Windows by stahování AzCopy selhalo, pokud by byl stažen jeden soubor nebo složka a název souboru nebo složky měl znak, který byl pro cestu systému Windows neplatný. To jsme opravili. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Ve výjimečných případech, při provádění přejmenování sdílené složky nebo přejmenování ve sdílené složce, pokud se nezdařily kopie přejmenování nebo pokud storage explore nemohl potvrdit úspěch kopií s Azure, existoval potenciál pro Průzkumníka úložišť k odstranění původní soubory před dokončením kopírování. To jsme opravili.

### <a name="new"></a>Nová

* Integrovaná verze AzCopy byla aktualizována na verzi 10.1.0.
* Ctrl/Cmd+R lze nyní použít k aktualizaci aktuálně zaměřeného editoru. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Verze rozhraní API úložiště zásobníku Azure byla změněna na 2017-04-17.
* Dialogové okno Spravovat přístup pro ADLS Gen2 nyní zachová masku synchronizovanou podobným způsobem jako jiné nástroje oprávnění POSIX. Uživatelské rozhraní vás také upozorní, pokud je provedena změna, která způsobí, že oprávnění uživatele nebo skupiny překročit hranice Mask. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Pro nahrávání AzCopy je příznak pro výpočet a nastavení hodnoty hash MD5 nyní povolen. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkce Preview

* Přihlášení ke toku kódu zařízení je nyní k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použít přihlášení k kódu zařízení". Doporučujeme všem uživatelům, kteří měli problémy s prázdnými přihlašovacími okny, aby tuto funkci vyzkoušeli, protože se může ukázat jako spolehlivější forma přihlášení.
* Průzkumník úložiště integrovaný s AzCopy je v současné době k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použijte AzCopy pro lepší nahrávání a stahování objektů Blob". Převody objektů BLOB dokončené azCopy by měly být rychlejší a výkonnější.

### <a name="fixes"></a>Opravy

* Dialogové okno Zásady přístupu již nenastaví datum vypršení platnosti v zásadách přístupu k úložišti, které nemají vypršení platnosti. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Některé změny byly provedeny v dialogovém okně Generovat SAS a ujistěte se, že uložené zásady přístupu jsou používány správně při generování SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Při pokusu o nahrání souboru se zarovnaným do objektu blob stránky bez 512 bajtů teď Průzkumník úložiště odhalí relevantnější chybu. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopírování kontejneru objektů Blob, který využíval zobrazovaný název, by se nezdaří. Nyní se používá skutečný název kontejneru blob. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Pokus o provedení určitých akcí ve složce ADLS Gen2, která měla znaky unicode v názvu, by se nezdaří. Všechny akce by nyní měly fungovat. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování blob bez AzCopy není ověřeno MD5 pro velké soubory. Důvodem je chyba v sada SDK úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Při pokusu o přístup k objektům BLOB ADLS Gen2 za proxy může selhat.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)

## <a name="version-180"></a>Verze 1.8.0
1. 5. 2019

### <a name="new"></a>Nová

* Integrovaná verze AzCopy byla aktualizována na verzi 10.1.0.
* Ctrl/Cmd+R lze nyní použít k aktualizaci aktuálně zaměřeného editoru. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Verze rozhraní API úložiště zásobníku Azure byla změněna na 2017-04-17.
* Dialogové okno Spravovat přístup pro ADLS Gen2 nyní zachová masku synchronizovanou podobným způsobem jako jiné nástroje oprávnění POSIX. Uživatelské rozhraní vás také upozorní, pokud je provedena změna, která způsobí, že oprávnění uživatele nebo skupiny překročit hranice Mask. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Pro nahrávání AzCopy je příznak pro výpočet a nastavení hodnoty hash MD5 nyní povolen. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkce Preview

* Přihlášení ke toku kódu zařízení je nyní k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použít přihlášení k kódu zařízení". Doporučujeme všem uživatelům, kteří měli problémy s prázdnými přihlašovacími okny, aby tuto funkci vyzkoušeli, protože se může ukázat jako spolehlivější forma přihlášení.
* Průzkumník úložiště integrovaný s AzCopy je v současné době k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použijte AzCopy pro lepší nahrávání a stahování objektů Blob". Převody objektů BLOB dokončené azCopy by měly být rychlejší a výkonnější.

### <a name="fixes"></a>Opravy

* Dialogové okno Zásady přístupu již nenastaví datum vypršení platnosti v zásadách přístupu k úložišti, které nemají vypršení platnosti. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Některé změny byly provedeny v dialogovém okně Generovat SAS a ujistěte se, že uložené zásady přístupu jsou používány správně při generování SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Při pokusu o nahrání souboru se zarovnaným do objektu blob stránky bez 512 bajtů teď Průzkumník úložiště odhalí relevantnější chybu. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopírování kontejneru objektů Blob, který využíval zobrazovaný název, by se nezdaří. Nyní se používá skutečný název kontejneru blob. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Pokus o provedení určitých akcí ve složce ADLS Gen2, která měla znaky unicode v názvu, by se nezdaří. Všechny akce by nyní měly fungovat. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování blob bez AzCopy není ověřeno MD5 pro velké soubory. Důvodem je chyba v sada SDK úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Při pokusu o přístup k objektům BLOB ADLS Gen2 za proxy může selhat.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumníka úložišť na Linuxu vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace naleznete v [průvodci odstraňováním potíží průzkumníka úložiště.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)

## <a name="version-170"></a>Verze 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Stažení Průzkumníka úložiště Azure 1.7.0
- [Azure Storage Explorer 1.7.0 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Nyní můžete změnit vlastníka a vlastnící skupinu při správě přístupu pro kontejner, soubor nebo složku ADLS Gen2.
* V systému Windows je nyní aktualizace Průzkumníka úložiště z produktu přírůstkovou instalací. To by mělo vést k rychlejší aktualizaci. Pokud dáváte přednost čisté instalaci, můžete si [stáhnout instalační program](https://azure.microsoft.com/features/storage-explorer/) sami a nainstalovat ručně. #1089

### <a name="preview-features"></a>Funkce Preview

* Přihlášení ke toku kódu zařízení je nyní k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použít přihlášení k kódu zařízení". Doporučujeme všem uživatelům, kteří měli problémy s prázdnými přihlašovacími okny, aby tuto funkci vyzkoušeli, protože se může ukázat jako spolehlivější forma přihlášení. #938
* Průzkumník úložiště integrovaný s AzCopy je v současné době k dispozici pro náhled. Chcete-li jej povolit, přejděte na "Náhled" → "Použijte AzCopy pro lepší nahrávání a stahování objektů Blob". Převody objektů BLOB dokončené azCopy by měly být rychlejší a výkonnější.

### <a name="fixes"></a>Opravy

* Nyní můžete zvolit typ objektu blob, který chcete nahrát, jako když je povolena azCopy. #1111
* Dříve, pokud jste povolili statické weby pro účet úložiště ADLS Gen2 a pak jej připojili s názvem a klíčem, Průzkumník úložiště by nezjistil, že byl povolen hierarchický obor názvů. To jsme opravili. #1081
* V editoru objektů blob bylo přerušeno řazení podle zbývajících dnů uchovávání nebo stavu. To jsme opravili. #1106
* Po 1.5.0, Průzkumník úložiště již čekal na dokončení kopií na straně serveru před hlášením úspěchu během přejmenování nebo kopírování & vložení. To jsme opravili. #976
* Při použití experimentální funkce AzCopy nebyl příkaz zkopírován po kliknutí na tlačítko "Příkaz kopírovat do schránky" sám o sobě vždy spustitelný. Nyní budou zkopírovány všechny příkazy potřebné ke spuštění přenosu ručně. #1079
* Dříve objekty BLOB ADLS Gen2 nebyly přístupné, pokud jste byli za proxy serverem. Důvodem byla chyba v nové síťové knihovně používané sadou Storage SDK. V 1.7.0 byl proveden pokus o zmírnění tohoto problému, ale někteří lidé mohou i nadále vidět problémy. Úplná oprava bude vydána v budoucí aktualizaci. #1090
* V položce 1.7.0 si dialogové okno pro uložení souboru správně zapamatuje poslední umístění, do které jste soubor uložili. #16
* V panelu vlastností byla úroveň SKU účtu úložiště zobrazena jako druh účtu. To jsme opravili. #654
* Někdy nebylo možné přerušit zapůjčení objektu blob, i když jste správně zadali název objektu blob. To jsme opravili. #1070

### <a name="known-issues"></a>Známé problémy

* Při použití RBAC, Průzkumník úložiště vyžaduje některá oprávnění vrstvy správy pro přístup k prostředkům úložiště. Další informace najdete v [příručce pro řešení potíží.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Při pokusu o přístup k objektům BLOB ADLS Gen2 za proxy může selhat.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete #537.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Verze 1.6.2
1/9/2019

### <a name="hotfixes"></a>Opravy hotfix
* V 1.6.1 entity přidané do ADLS Gen2 ACLs podle ObjectId, které nebyly uživateli byly vždy přidány jako skupiny. Nyní jsou jako skupiny přidány pouze skupiny a entity, jako jsou podnikové aplikace a objekty zabezpečení služeb, jsou přidány jako uživatelé. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Pokud účet Úložiště Gen2 ADLS neměl žádné kontejnery a byl připojen s názvem a klíčem, průzkumník úložiště by nezjistil, že účet úložiště byl ADLS Gen2. To jsme opravili. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* V 1.6.0 konflikty během kopírování a vkládání nebude výzva k řešení. Místo toho by konfliktní kopie jednoduše selhala. Nyní, v prvním konfliktu, budete dotázáni, jak byste chtěli, aby byl vyřešen. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Z důvodu omezení rozhraní API byla zakázána všechna ověření objektových identifikátorů v dialogovém okně Spravovat přístup. K ověření nyní dojde pouze pro uživatelské upneky. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně Správa přístupu ADLS Gen2 nelze změnit oprávnění pro skupinu. To jsme opravili. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidána podpora přetahování nahrávání do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně vlastností pro soubory a složky ADLS Gen2 někdy chyběla '/'. To jsme opravili. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nezdaří získání aktuálních oprávnění pro kontejner, soubor nebo složku ADLS Gen2, je nyní chyba správně zobrazena v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH v systému Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno Připojit se nyní zobrazí správně, pokud nejsou přihlášeni žádní přihlášení uživatelé a nebyly připojeny žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládání vlastností pro non-HNS objekty BLOB a soubory by kódovat hodnotu každé vlastnosti. To mělo za následek zbytečné kódování hodnot, které obsahovaly pouze znaky ASCII. Nyní budou hodnoty kódovány pouze v případě, že obsahují znaky bez ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů blob jiného než HNS by se nezdaří, pokud by byl použit SAS a SAS neměl oprávnění ke čtení. To jsme opravili. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. To jsme opravili. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by se nezdaří při pokusu o stažení složky z kontejneru objektů blob ADLS Gen2, pokud složka měla mezery v názvu. To jsme opravili. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl přerušen v 1.6.0. Nyní je opravena. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Teď můžete použít Průzkumníka úložiště pro přístup k datům objektů Blob přes [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník úložiště nemůže načíst klíče pro váš účet úložiště, pak token OAuth se použije k ověření při interakci s daty.
* Průzkumník úložiště nyní podporuje účty úložiště ADLS Gen2. Když Průzkumník úložiště zjistí, že hierarchický obor názvů je povolen pro účet úložiště, zobrazí se vedle názvu vašeho účtu úložiště "(ADLS Gen2 Preview)". Průzkumník úložiště dokáže zjistit, jestli je hierarchický obor názvů povolen, když jste přihlášeni, nebo jestli jste připojili účet úložiště s názvem a klíčem. U účtů úložiště ADLS Gen2 můžete pomocí Průzkumníka úložiště:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností a oprávnění kontejneru (levá strana)
  * Zobrazení a navigace v datech uvnitř kontejnerů
  * Vytvoření nových složek
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění souborů a složek (pravá strana).
    
    Jiné typické funkce objektu blob, jako je například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je k dispozici pouze při přihlášení. Kromě toho při práci v účtu Úložiště ADLS Gen2, Průzkumník úložiště bude používat AzCopy pro všechny obrázky a stahování a výchozí použití názvu a klíče pověření pro všechny operace, pokud je k dispozici.
* Po silné zpětné vazby od uživatelů, break lease lze znovu použít k přerušení zapůjčení na více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Při stahování z účtu ADLS Gen2 Storage, pokud jeden z přenesených souborů již existuje, pak AzCopy někdy dojde k chybě. Tato oprava bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Verze 1.6.1
12/18/2018

### <a name="hotfixes"></a>Opravy hotfix
* Z důvodu omezení rozhraní API byla zakázána všechna ověření objektových identifikátorů v dialogovém okně Spravovat přístup. K ověření nyní dojde pouze pro uživatelské upneky. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně Správa přístupu ADLS Gen2 nelze změnit oprávnění pro skupinu. To jsme opravili. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidána podpora přetahování nahrávání do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně vlastností pro soubory a složky ADLS Gen2 někdy chyběla '/'. To jsme opravili. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nezdaří získání aktuálních oprávnění pro kontejner, soubor nebo složku ADLS Gen2, je nyní chyba správně zobrazena v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH v systému Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno Připojit se nyní zobrazí správně, pokud nejsou přihlášeni žádní přihlášení uživatelé a nebyly připojeny žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládání vlastností pro non-HNS objekty BLOB a soubory by kódovat hodnotu každé vlastnosti. To mělo za následek zbytečné kódování hodnot, které obsahovaly pouze znaky ASCII. Nyní budou hodnoty kódovány pouze v případě, že obsahují znaky bez ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů blob jiného než HNS by se nezdaří, pokud by byl použit SAS a SAS neměl oprávnění ke čtení. To jsme opravili. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. To jsme opravili. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by se nezdaří při pokusu o stažení složky z kontejneru objektů blob ADLS Gen2, pokud složka měla mezery v názvu. To jsme opravili. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl přerušen v 1.6.0. Nyní je opravena. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Teď můžete použít Průzkumníka úložiště pro přístup k datům objektů Blob přes [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník úložiště nemůže načíst klíče pro váš účet úložiště, pak token OAuth se použije k ověření při interakci s daty.
* Průzkumník úložiště nyní podporuje účty úložiště ADLS Gen2. Když Průzkumník úložiště zjistí, že hierarchický obor názvů je povolen pro účet úložiště, zobrazí se vedle názvu vašeho účtu úložiště "(ADLS Gen2 Preview)". Průzkumník úložiště dokáže zjistit, jestli je hierarchický obor názvů povolen, když jste přihlášeni, nebo jestli jste připojili účet úložiště s názvem a klíčem. U účtů úložiště ADLS Gen2 můžete pomocí Průzkumníka úložiště:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností a oprávnění kontejneru (levá strana)
  * Zobrazení a navigace v datech uvnitř kontejnerů
  * Vytvoření nových složek
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění souborů a složek (pravá strana).
    
    Jiné typické funkce objektu blob, jako je například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je k dispozici pouze při přihlášení. Kromě toho při práci v účtu Úložiště ADLS Gen2, Průzkumník úložiště bude používat AzCopy pro všechny obrázky a stahování a výchozí použití názvu a klíče pověření pro všechny operace, pokud je k dispozici.
* Po silné zpětné vazby od uživatelů, break lease lze znovu použít k přerušení zapůjčení na více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Při stahování z účtu ADLS Gen2 Storage, pokud jeden z přenesených souborů již existuje, pak AzCopy někdy dojde k chybě. Tato oprava bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Verze 1.6.0
12/5/2018

### <a name="new"></a>Nová

* Teď můžete použít Průzkumníka úložiště pro přístup k datům objektů Blob přes [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník úložiště nemůže načíst klíče pro váš účet úložiště, pak token OAuth se použije k ověření při interakci s daty.
* Průzkumník úložiště nyní podporuje účty úložiště ADLS Gen2. Když Průzkumník úložiště zjistí, že hierarchický obor názvů je povolen pro účet úložiště, zobrazí se vedle názvu vašeho účtu úložiště "(ADLS Gen2 Preview)". Průzkumník úložiště dokáže zjistit, jestli je hierarchický obor názvů povolen, když jste přihlášeni, nebo jestli jste připojili účet úložiště s názvem a klíčem. U účtů úložiště ADLS Gen2 můžete pomocí Průzkumníka úložiště:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností a oprávnění kontejneru (levá strana)
  * Zobrazení a navigace v datech uvnitř kontejnerů
  * Vytvoření nových složek
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění souborů a složek (pravá strana).
    
    Jiné typické funkce objektu blob, jako je například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je k dispozici pouze při přihlášení. Kromě toho při práci v účtu Úložiště ADLS Gen2, Průzkumník úložiště bude používat AzCopy pro všechny obrázky a stahování a výchozí použití názvu a klíče pověření pro všechny operace, pokud je k dispozici.
* Po silné zpětné vazby od uživatelů, break lease lze znovu použít k přerušení zapůjčení na více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Při stahování z účtu ADLS Gen2 Storage, pokud jeden z přenesených souborů již existuje, pak AzCopy někdy dojde k chybě. Tato oprava bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Verze 1.5.0
10/29/2018

### <a name="new"></a>Nová

* Nyní můžete použít [AzCopy v10 (Náhled)](https://github.com/Azure/azure-storage-azcopy) pro nahrávání a stahování objektů Blob. Chcete-li tuto funkci povolit, přejděte do nabídky "Experimentální" a poté klepněte na tlačítko "Použít azCopy pro vylepšené nahrávání a stahování objektů Blob". Pokud je povolena, AzCopy bude použit v následujících scénářích:
   * Nahrávání složek a souborů do kontejnerů objektů blob, buď přes panel nástrojů nebo přetažení.
   * Stahování složek a souborů, a to buď prostřednictvím panelu nástrojů nebo kontextové nabídky.

* Navíc při použití AzCopy:
   * Příkaz AzCopy použitý k provedení přenosu do schránky můžete zkopírovat. Jednoduše klikněte na "Kopírovat příkaz AzCopy do schránky" v protokolu aktivit.
   * Po nahrání budete muset editor objektů blob aktualizovat ručně.
   * Nahrávání souborů do připojit objekty BLOB není podporováno a soubory vhd se nahrají jako objekty BLOB stránky a všechny ostatní soubory budou odeslány jako objekty BLOB bloku.
   * Chyby a konflikty, ke kterým dojde během nahrávání nebo stahování, se objeví až po dokončení nahrávání nebo stahování.

Nakonec bude v budoucnu přicházet podpora pro používání AzCopy se sdílenými složkami.
* Storage Explorer nyní používá Electron verze 2.0.11.
* Porušení zapůjčení lze nyní provádět pouze na jeden objekt blob najednou. Kromě toho budete muset zadat název objektu blob, jehož zapůjčení je prolomení. Tato změna byla provedena ke snížení pravděpodobnosti náhodného porušení zapůjčení, zejména pro virtuální chod. #394
* Pokud se někdy setkáte s problémy s přihlášením, můžete nyní zkusit resetovat ověřování. Přejděte do nabídky "Nápověda" a klikněte na tlačítko "Obnovit" pro přístup k této funkci. #419

### <a name="fix"></a>Oprava

* Po silné zpětné vazbě od uživatelů byl znovu povolen výchozí uzel emulátoru. Další připojení emulátoru můžete přidat prostřednictvím dialogového okna Připojit, ale pokud je emulátor nakonfigurován tak, aby používal výchozí porty, můžete také použít uzel Emulátor * výchozí porty v části "Místní & připojené/účty úložiště". #669
* Průzkumník úložiště již nebude vám umožní nastavit hodnoty metadat objektu blob, které mají úvodní nebo koncové prázdné znaky. #760
* Tlačítko "Přihlásit se" bylo vždy povoleno na stejných stránkách dialogového okna Připojit. V případě potřeby je nyní zakázán. #761
* Rychlý přístup již nebude generovat chybu v konzole, pokud nebyly přidány žádné položky rychlého přístupu.

### <a name="known-issues"></a>Známé problémy

* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v #537.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokujete vás, komentujte tento problém.
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu. Chcete-li tento problém vyřešit při nahrávání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkci AzCopy.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s prostředky Azure Stack může mít za následek neočekávané chyby.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Verze 1.4.4
10/15/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět k odblokování uživatelů Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání přadleny jsou nyní pomocí CSS animace ke snížení množství GPU používá Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externích prostředků, například pro připojení SAS a emulátory, byly výrazně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který připojujete. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k více místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky do rychlého přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník úložiště nyní podporuje funkci Obnovitelné odstranění. Můžete:
   * Nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem myši na uzel Kontejnery objektů blob pro váš účet úložiště.
   * Zobrazte obnovitelné odstraněné objekty BLOB v Editoru objektů blob tak, že v rozevíracím přehledu vedle navigačního panelu vyberete aktivní a odstraněné objekty BLOB.
   * Zrušte odstranění měkkých odstraněných objektů BLOB.

### <a name="fixes"></a>Opravy
* Akce Konfigurace nastavení CORS již není na účtech úložiště Premium k dispozici, protože účty úložiště Premium nepodporují cors. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je nyní k dispozici vlastnost Sdílený přístupový podpis. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je teď dostupná pro účty úložiště Blob a GPV2, které byly připnuté k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých ohledech se Průzkumníku úložiště nepodaří zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například Emulátor úložiště Azure nebo Azurite, budete muset mít je poslouchat připojení na jejich výchozí porty. V opačném případě průzkumníka úložiště nebude možné připojit k nim.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Verze 1.4.3
10/11/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět k odblokování uživatelů Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání přadleny jsou nyní pomocí CSS animace ke snížení množství GPU používá Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externích prostředků, například pro připojení SAS a emulátory, byly výrazně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který připojujete. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k více místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky do rychlého přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník úložiště nyní podporuje funkci Obnovitelné odstranění. Můžete:
   * Nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem myši na uzel Kontejnery objektů blob pro váš účet úložiště.
   * Zobrazte obnovitelné odstraněné objekty BLOB v Editoru objektů blob tak, že v rozevíracím přehledu vedle navigačního panelu vyberete aktivní a odstraněné objekty BLOB.
   * Zrušte odstranění měkkých odstraněných objektů BLOB.

### <a name="fixes"></a>Opravy
* Akce Konfigurace nastavení CORS již není na účtech úložiště Premium k dispozici, protože účty úložiště Premium nepodporují cors. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je nyní k dispozici vlastnost Sdílený přístupový podpis. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je teď dostupná pro účty úložiště Blob a GPV2, které byly připnuté k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých ohledech se Průzkumníku úložiště nepodaří zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například Emulátor úložiště Azure nebo Azurite, budete muset mít je poslouchat připojení na jejich výchozí porty. V opačném případě průzkumníka úložiště nebude možné připojit k nim.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Verze 1.4.2
09/24/2018

### <a name="hotfixes"></a>Opravy hotfix
* Aktualizujte verzi rozhraní API azure pro správu prostředků na 2018-07-01 a přidejte podporu pro nové druhy účtů úložiště Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nová
* Přílohy externích prostředků, například pro připojení SAS a emulátory, byly výrazně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který připojujete. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k více místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky do rychlého přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník úložiště nyní podporuje funkci Obnovitelné odstranění. Můžete:
   * Nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem myši na uzel Kontejnery objektů blob pro váš účet úložiště.
   * Zobrazte obnovitelné odstraněné objekty BLOB v Editoru objektů blob tak, že v rozevíracím přehledu vedle navigačního panelu vyberete aktivní a odstraněné objekty BLOB.
   * Zrušte odstranění měkkých odstraněných objektů BLOB.

### <a name="fixes"></a>Opravy
* Akce Konfigurace nastavení CORS již není na účtech úložiště Premium k dispozici, protože účty úložiště Premium nepodporují cors. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je nyní k dispozici vlastnost Sdílený přístupový podpis. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je teď dostupná pro účty úložiště Blob a GPV2, které byly připnuté k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých ohledech se Průzkumníku úložiště nepodaří zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například Emulátor úložiště Azure nebo Azurite, budete muset mít je poslouchat připojení na jejich výchozí porty. V opačném případě průzkumníka úložiště nebude možné připojit k nim.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Verze 1.4.1
08/28/2018

### <a name="hotfixes"></a>Opravy hotfix
* Při prvním spuštění průzkumníka úložiště nebyl schopen vygenerovat klíč používaný k šifrování citlivých dat. To by způsobilo problémy při použití rychlého přístupu a připojení prostředků. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Pokud váš účet nevyžadoval vícefaktorové informace pro svého domovského tenanta, ale pro některé další klienty, Průzkumník úložiště nebude moci vypsat předplatná. Nyní po přihlášení pomocí takového účtu, Průzkumník úložiště vás vyzve k opětovnému zadání pověření a provedení vícefaktorové informace. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Průzkumník úložiště nemohl připojit prostředky z Azure Germany a Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Pokud jste se přihlásili ke dvěma účtům, které měly stejnou e-mailovou adresu, Průzkumník úložiště někdy nezobrazí vaše prostředky ve stromovém zobrazení. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na pomalejších počítačích se systémem Windows by úvodní obrazovka někdy trvat značné množství času se zobrazí. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Dialogové okno připojit se zobrazí i v případě, že byly připojeny účty nebo služby. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nová
* Přílohy externích prostředků, například pro připojení SAS a emulátory, byly výrazně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který připojujete. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k více místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky do rychlého přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník úložiště nyní podporuje funkci Obnovitelné odstranění. Můžete:
   * Nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem myši na uzel Kontejnery objektů blob pro váš účet úložiště.
   * Zobrazte obnovitelné odstraněné objekty BLOB v Editoru objektů blob tak, že v rozevíracím přehledu vedle navigačního panelu vyberete aktivní a odstraněné objekty BLOB.
   * Zrušte odstranění měkkých odstraněných objektů BLOB.

### <a name="fixes"></a>Opravy
* Akce Konfigurace nastavení CORS již není na účtech úložiště Premium k dispozici, protože účty úložiště Premium nepodporují cors. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je nyní k dispozici vlastnost Sdílený přístupový podpis. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je teď dostupná pro účty úložiště Blob a GPV2, které byly připnuté k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých ohledech se Průzkumníku úložiště nepodaří zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například Emulátor úložiště Azure nebo Azurite, budete muset mít je poslouchat připojení na jejich výchozí porty. V opačném případě průzkumníka úložiště nebude možné připojit k nim.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Verze 1.3.0
07/09/2018

### <a name="new"></a>Nová
* Přístup ke $web kontejnerům používaným statickými weby je nyní podporován. To vám umožní snadno nahrávat a spravovat soubory a složky používané na vašich webových stránkách. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Panel aplikací v macOS byl reorganizován. Změny zahrnují nabídku Soubor, některé změny klávesových zkratek a několik nových příkazů v nabídce aplikace. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Koncový bod autority pro přihlášení k Azure US Government byl změněn nahttps://login.microsoftonline.us/
* Usnadnění přístupu: Pokud je aktivní program pro čtení z obrazovky, navigace pomocí klávesnice nyní funguje s tabulkami používanými k zobrazení položek na pravé straně. Pomocí kláves se šipkami můžete procházet řádky a sloupce, Enter vyvolat výchozí akce, klávesa kontextové nabídky otevřít kontextovou nabídku pro položku a Posun nebo Řízení pro vícenásobné výběry. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Opravy
*  Na některých strojích, dětské procesy byly dlouho začít. Když k tomu dojde, zobrazí se chyba "podřízený proces se nepodařilo spustit včas". Čas přidělený pro spuštění podřízeného procesu byl nyní zvýšen z 20 na 90 sekund. Pokud se vás tento problém stále týká, komentujte problém s propojeným GitHubem. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Při použití SAS, který neměl oprávnění ke čtení, nebylo možné nahrát velký objekt blob. Logika pro nahrávání byla upravena tak, aby fungovala v tomto scénáři. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Nastavení úrovně veřejného přístupu pro kontejner by odebrat všechny zásady přístupu a naopak. Nyní úroveň veřejného přístupu a přístup zásady jsou zachovány při nastavení jedné z těchto dvou. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" byl zkrácen v dialogovém okně Vlastnosti. To jsme opravili. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* V dialogovém okně Vytvořit nový adresář chyběla předpona "Průzkumník úložiště Microsoft Azure -". To jsme opravili. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Usnadnění přístupu: Při použití VoiceOveru bylo obtížné navigovat dialogové okno Přidat entitu. Bylo dosaženo zlepšení. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Usnadnění přístupu: Barva pozadí tlačítka sbalit nebo rozbalit pro podokno Akce a vlastnosti nebyla konzistentní s podobnými ovládacími prvky ui v motivu Černá s vysokým kontrastem. Barva byla změněna. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Usnadnění přístupu: V černém motivu s vysokým kontrastem nebyl zobrazen styl fokusu tlačítka X v dialogovém okně Vlastnosti. To jsme opravili. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Usnadnění přístupu: Na kartách Akce a Vlastnosti chybělo několik hodnot árie, což vedlo k podřadné obrazovce. Chybějící hodnoty árie byly nyní přidány. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Usnadnění přístupu: Sbalené uzly stromu na levé straně nebyly uvedeny aria-rozšířené hodnoty false. To jsme opravili. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Známé problémy
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém vyřešit, stačí aktualizovat uzel skupiny. Další informace naleznete v [tomto problému.](https://github.com/Microsoft/AzureStorageExplorer/issues/537)
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Azure Stack nepodporuje následující funkce a pokus o jejich použití při práci s Azure Stack může mít za následek neočekávané chyby:
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Verze 1.2.0
06/12/2018

### <a name="new"></a>Nová
* Pokud Průzkumník úložiště se nezdaří načíst odběry pouze z podmnožiny vašich klientů, pak všechny úspěšně načtené odběry se zobrazí spolu s chybovou zprávou speciálně pro klienty, kteří selhali. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* V systému Windows, pokud je k dispozici aktualizace, můžete nyní zvolit "Aktualizovat na zavřít". Po výběru této možnosti se instalační program aktualizace spustí po zavření Průzkumníka úložiště. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Snímek obnovení byl přidán do kontextové nabídky editoru sdílené složky při zobrazení snímku sdílené složky. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Tlačítko Vymazat frontu je nyní vždy povoleno. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Podpora pro přihlášení do služby ADFS Azure Stack byla znovu povolena. Azure Stack verze 1804 nebo vyšší je vyžadována. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Opravy
* Pokud jste si prohlíželi snímky sdílené složky, jejichž název byl předponou jiné sdílené složky ve stejném účtu úložiště, budou také uvedeny snímky pro druhou sdílenou složku. Tento problém byl opraven. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Při připojení přes SAS, obnovení souboru ze snímku sdílené složky by mělo za následek chybu. Tento problém byl opraven. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Při zobrazení snímků pro objekt blob byla akce Zvýšit snímek povolena, když byl vybrán základní objekt blob a jeden snímek. Akce je nyní povolena pouze v případě, že je vybrán jeden snímek. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Pokud byla spuštěna jedna úloha (například stažení objektu blob) a později se nezdařila, nebude automaticky opakovat, dokud nespustíte jinou úlohu stejného typu. Všechny úlohy by nyní měly automaticky opakovat, bez ohledu na to, kolik úloh jste zařazovali do fronty.
* Editory otevřené pro nově vytvořené kontejnery objektů blob v účtech GPV2 a úložiště objektů blob neměl sloupec vrstvy přístupu. Tento problém byl opraven. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Sloupec vrstvy přístupu se někdy nezobrazí, když byl účet úložiště nebo kontejner objektů blob připojen prostřednictvím SAS. Sloupec se nyní vždy zobrazí, ale s prázdnou hodnotou, pokud není nastavena žádná úroveň přístupu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Nastavení úrovně přístupu nově nahraného objektu blob bloku bylo zakázáno. Tento problém byl opraven. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Pokud bylo pomocí klávesnice vyvoláno tlačítko "Keep Tab Open", došlo by ke ztrátě fokusu klávesnice. Nyní se pozornost přesune na kartu, která byla otevřená. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Pro dotaz v Tvůrce dotazů VoiceOver neposkytuje použitelný popis aktuálního operátoru. Nyní je popisnější. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Stránkování odkazy pro různé editory nebyly popisné. Byly změněny tak, aby byly popisnější. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* V dialogovém okně Přidat entitu VoiceOver neoznamoval, do kterého sloupce byl vstupní prvek součástí. Název aktuálního sloupce je nyní součástí popisu prvku. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Přepínací tlačítka a zaškrtávací políčka neměly viditelné ohraničení při zaostření. Tento problém byl opraven. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například Emulátor úložiště Azure nebo Azurite, budete muset mít je poslouchat připojení na jejich výchozí porty. V opačném případě průzkumníka úložiště nebude možné připojit k nim.
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Verze 1.1.0
05/09/2018

### <a name="new"></a>Nová
* Průzkumník úložiště nyní podporuje použití Azuritu. Poznámka: připojení k Azuritu je pevně zakódováno k výchozím koncovým bodům vývoje.
* Průzkumník úložiště teď podporuje vrstvy přístupu pro účty úložiště pouze pro objekt y blob a GPV2. Další informace o přístupových úrovních [najdete zde](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Při generování sas již není potřeba čas zahájení.

### <a name="fixes"></a>Opravy
* Načítání předplatného pro účty vlády USA bylo přerušeno. Tento problém byl opraven. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Doba vypršení platnosti zásad přístupu nebyla správně uložena. Tento problém byl opraven. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Při generování adresy URL SAS pro položku v kontejneru nebyl název položky připojen k adrese URL. Tento problém byl opraven. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Při vytváření SAS, vypršení platnosti, které jsou v minulosti by někdy výchozí hodnotu. To bylo způsobeno Průzkumníka úložiště pomocí poslední použité počáteční a vypršení času jako výchozí hodnoty. Nyní při každém otevření dialogového okna SAS je generována nová sada výchozích hodnot. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Při kopírování mezi účty úložiště je generována 24hodinová Služba SAS. Pokud kopie trvala déle než 24 hodin, kopie by se nezdaří. Zvýšili jsme SAS na poslední 1 týden, abychom snížili pravděpodobnost selhání kopie kvůli prošlé SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* U některých aktivit by kliknutí na "Zrušit" vždy nefungovalo. Tento problém byl opraven. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* U některých aktivit byla přenosová rychlost chybná. Tento problém byl opraven. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Pravopis "Předchozí" v nabídce Zobrazení byl chybný. Nyní je správně napsáno. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Poslední stránka instalačního programu systému Windows měla tlačítko "Další". Bylo změněno na tlačítko Dokončit. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Fokus tabulátoru nebyl viditelný pro tlačítka v dialogových oknech při použití motivu HC Black. Nyní je vidět. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Caseing "Auto-Resolve" pro akce v protokolu aktivit byl chybný. Nyní je to správné. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Při odstraňování entity z tabulky se v dialogovém okně s žádostí o potvrzení zobrazí ikona chyby. Dialogové okno nyní používá ikonu upozornění. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte VS for Mac a někdy jste vytvořili vlastní konfiguraci AAD, nemusí být možné se přihlásit. Chcete-li problém vyřešit, odstraňte obsah ~/. IdentityService/AadKonfigurace. Pokud tak neučiníte, neodblokuje vás, prosím, komentář k [tomuto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurit ještě plně neimplementoval všechna úložiště API. Z tohoto důvodu může být neočekávané chyby nebo chování při použití Azurite pro vývoj úložiště.
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Nahrávání ze složky OneDrive nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě nebyla integrována do Electronu.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Verze 1.0.0
04/16/2018

### <a name="new"></a>Nová
* Rozšířené ověřování, které umožňuje Průzkumníkovi úložiště používat stejné úložiště účtů jako Visual Studio 2017. Chcete-li tuto funkci používat, budete se muset znovu přihlásit ke svým účtům a znovu nastavit filtrovaná předplatná.
* Pro účty Azure Stack podporované AAD, Průzkumník úložiště teď načte předplatná Azure Stack, když je povolen cílová služba Azure Stack. Již není nutné vytvářet vlastní přihlašovací prostředí.
* Bylo přidáno několik zkratek, které umožňují rychlejší navigaci. Patří mezi ně přepínání různých panelů a pohyb mezi editory. Další podrobnosti najdete v nabídce Zobrazení.
* Zpětná vazba Průzkumníka úložiště nyní žije na GitHubu. Na naši stránku s problémy se dostanete kliknutím na [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)tlačítko Zpětná vazba v levém dolním rohu nebo na stránce . Nebojte se předkládat návrhy, hlásit problémy, klást otázky nebo zanechat jakoukoli jinou formu zpětné vazby.
* Pokud narážíte na problémy s certifikátem TLS/SSL a nemůžete najít problematický certifikát, můžete nyní spustit Průzkumníka úložiště z příkazového řádku s příznakem. `--ignore-certificate-errors` Při spuštění s tímto příznakem bude Průzkumník úložiště ignorovat chyby certifikátu TLS/SSL.
* V místní nabídce pro položky objektů blob a souborů je nyní možnost Stáhnout.
* Vylepšená podpora přístupnosti a čtečky obrazovky. Pokud spoléháte na funkce usnadnění přístupu, další informace najdete v naší dokumentaci k [usnadnění přístupu.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility)
* Průzkumník úložišť nyní používá Electron 1.8.3

### <a name="breaking-changes"></a>Zásadní změny
* Průzkumník úložiště přešel na novou knihovnu ověřování. Jako součást přechodu do knihovny se budete muset znovu přihlásit ke svým účtům a znovu nastavit filtrovaná předplatná
* Metoda používaná k šifrování citlivých dat byla změněna. To může vést k tomu, že některé položky rychlého přístupu je třeba znovu přidat a/nebo některé z vás připojených prostředků, které je třeba znovu připojit.

### <a name="fixes"></a>Opravy
* Někteří uživatelé za proxy servery by měli skupinový blob nahrávání nebo stahování přerušeno chybovou zprávou Nelze vyřešit. Tento problém byl opraven.
* Pokud bylo při použití přímého odkazu potřeba přihlášení, kliknutím na výzvu "Přihlásit se" se zobrazí prázdný dialog. Tento problém byl opraven.
* Na Linuxu, pokud Storage Explorer není schopen spustit z důvodu selhání procesu GPU, budete nyní informováni o selhání, řekl, aby používal přepínač '-- disable-GPU' a Průzkumník úložiště se pak automaticky restartuje s přepínačem zapnutým.
* V dialogovém okně Zásady přístupu bylo obtížné identitovat neplatné zásady přístupu. Neplatná ID zásad přístupu jsou nyní označena červeně, aby byla větší viditelnost.
* Protokol aktivit by někdy velké plochy mezermezi různými částmi aktivity. Tento problém byl opraven.
* Pokud jste v editoru dotazů tabulky ponechali klauzuli časového razítka v neplatném stavu a poté jste se pokusili změnit jinou klauzuli, editor by zamrzl. Editor nyní obnoví klauzuli časového razítka do posledního platného stavu, když je zjištěna změna jiné klauzule.
* Pokud jste se při zadávání vyhledávacího dotazu ve stromovém zobrazení pozastavili, hledání by začalo a fokus by byl z textového pole ukraden. Nyní musíte explicitně začít hledat stisknutím klávesy "Enter" nebo kliknutím na tlačítko spustit vyhledávání.
* Příkaz Získat sdílený přístupový podpis bude někdy zakázán, když kliknete pravým tlačítkem myši na soubor ve sdílené složce. Tento problém byl opraven.
* Pokud byl uzel stromu prostředků s fokusem filtrován během hledání, nelze kartu do stromu prostředků a pomocí kláves se šipkami procházet strom prostředků. Nyní pokud je skrytý cílový uzel stromu prostředků, bude první uzel ve stromu prostředků automaticky zaměřen.
* Další oddělovač by někdy být viditelné v panelu nástrojů editoru. Tento problém byl opraven.
* Textové pole s popisem cesty by někdy přetekla. Tento problém byl opraven.
* Editory Blob a File Share se někdy neustále aktualizují při nahrávání mnoha souborů najednou. Tento problém byl opraven.
* Funkce Statistika složek neměla v zobrazení Správa snímků sdílené složky žádný účel. Nyní byl zakázán.
* V Linuxu se nabídka Soubor neobjevila. Tento problém byl opraven.
* Při nahrávání složky do sdílené složky byl ve výchozím nastavení odeslán pouze obsah složky. Výchozí chování je nyní nahrání obsahu složky do odpovídající složky ve sdílené složce.
* Pořadí tlačítek v několika dialogových oknech bylo obráceno. Tento problém byl opraven.
* Různé opravy související se zabezpečením.

### <a name="known-issues"></a>Známé problémy
* Ve výjimečných případech může dojít k uvíznutí fokusu stromu v rychlém přístupu. Chcete-li odlepit fokus, můžete aktualizovat vše.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele Linuxu budete muset nainstalovat [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Verze 0.9.6
02/28/2018

### <a name="fixes"></a>Opravy
* Problém zabránil očekávaným objektům BLOB/souborům uvedeným v editoru. Tento problém byl opraven.
* Problém způsobil přepínání mezi zobrazení snímků zobrazit položky nesprávně. Tento problém byl opraven.

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště nepodporuje účty Služby ADFS.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde [popsané](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Verze 0.9.5
02/06/2018

### <a name="new"></a>Nová

* Podpora snímků sdílených složek:
    * Vytvářejte a spravujte snímky sdílených složek.
    * Při zkoumání můžete snadno přepínat zobrazení mezi snímky sdílených složek.
    * Obnovte předchozí verze souborů.
* Podpora preview pro Azure Data Lake Store:
    * Připojte se k prostředkům ADLS napříč více účty.
    * Připojte se k prostředkům ADLS a sdílejte je pomocí identifikátorů ADL URI.
    * Provádět základní operace souborů a složek rekurzivně.
    * Připnutí jednotlivých složek do rychlého přístupu
    * Zobrazení statistiky složek.

### <a name="fixes"></a>Opravy
* Vylepšení výkonu při spuštění.
* Různé opravy chyb.

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště nepodporuje účty Služby ADFS.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Verze 0.9.4 a 0.9.3
01/21/2018

### <a name="new"></a>Nová
* Stávající okno Průzkumníka úložiště bude znovu použito, když:
    * Otevírání přímých odkazů generovaných v Průzkumníku úložiště.
    * Otevření Průzkumníka úložiště z portálu.
    * Otevření Průzkumníka úložiště z rozšíření Code služby Azure Storage VS (již brzy).
* Přidána možnost otevřít nové okno Průzkumníka úložiště z Průzkumníka úložiště.
    * Pro Windows je v nabídce soubora a v kontextové nabídce hlavního panelu možnost Nové okno.
    * Pro Mac je v nabídce aplikace možnost "Nové okno".

### <a name="fixes"></a>Opravy
* Byl opraven problém se zabezpečením. Upgradujte na 0.9.4 co nejdříve.
* Staré aktivity nebyly vhodně vyčištěny. To ovlivnilo výkon dlouho běžících úloh. Nyní jsou správně vyčištěny.
* Akce zahrnující velký počet souborů a adresářů by občas způsobit Storage Explorer zmrazit. Požadavky na Azure pro sdílené složky souborů jsou teď omezeny, aby se omezilo používání systémových prostředků.

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště nepodporuje účty Služby ADFS.
* Klávesové zkratky pro "View Explorer" a "View Account Management" by měly být Ctrl/Cmd+Shift+E a Ctrl/Cmd+Shift+A.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Verze 0.9.2
11/01/2017

### <a name="hotfixes"></a>Opravy hotfix
* Neočekávané změny dat byly možné při úpravách hodnot Edm.DateTime pro entity tabulky v závislosti na místním časovém pásmu. Editor nyní používá pole prostého textu, které poskytuje přesnou a konzistentní kontrolu nad hodnotami Edm.DateTime.
* Nahrávání nebo stahování skupiny objektů BLOB při připojení s názvem a klíčem se nespustí. Tento problém byl opraven.
* Dříve Průzkumník úložiště by vás pouze vyzvat k opětovnému ověření zastaralého účtu, pokud byl vybrán jeden nebo více předplatných účtu. Průzkumník úložiště vás teď vyzve, i když je účet plně odfiltrován.
* Doména koncových bodů pro Azure US Government byla chybná. Bylo to opraveno.
* Na tlačítko Použít na panelu Spravovat účty bylo někdy těžké kliknout. To by se už nemělo dít.

### <a name="new"></a>Nová
* Podpora preview pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytváření databází a kolekcí
    * Manipulace s daty
    * Dotazování, vytváření nebo odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelem definovaných funkcí nebo aktivačních událostí
    * Připojení k databázím a správa databází pomocí připojovacích řetězců
* Vylepšen výkon nahrávání/stahování mnoha malých objektů BLOB.
* Přidána akce "Opakovat vše", pokud jsou ve skupině pro odesílání objektů blob nebo skupině pro stahování objektů blob chyby.
* Průzkumník úložiště teď pozastaví iteraci během nahrávání a stahování objektů blob, pokud zjistí, že došlo ke ztrátě síťového připojení. Po obnovení síťového připojení pak můžete pokračovat v iteraci.
* Přidána možnost "Zavřít vše", "Zavřít ostatní", a "Zavřít" karty přes kontextové menu.
* Průzkumník úložiště teď používá nativní dialogy a nativní místní nabídky.
* Průzkumník úložiště je teď přístupnější. Mezi vylepšení patří:
    * Vylepšená podpora čtečky obrazovky pro NVDA ve Windows a pro VoiceOver na Macu
    * Vylepšené téma s vysokým kontrastem
    * Opravy tabulátorů a fokusu klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt blob s neplatným názvem souboru systému Windows, operace by se nezdaří. Průzkumník úložiště teď zjistí, jestli je název objektu blob neplatný, a zeptá se, jestli ho chcete buď zakódovat, nebo přeskočit objekt blob. Průzkumník úložiště také zjistí, zda se název souboru zdá být zakódován, a zeptá se vás, zda jej chcete před nahráním dekódovat.
* Během nahrávání objektů blob by se někdy editor cílového kontejneru objektů blob neaktualizoval správně. Tento problém byl opraven.
* Podpora pro několik forem připojovacích řetězců a identifikátorů URI SAS se vrátila. Vyřešili jsme všechny známé problémy, ale pokud narazíte na další problémy, pošlete nám prosím zpětnou vazbu.
* Oznámení o aktualizaci bylo přerušeno pro některé uživatele v 0.9.0. Tento problém byl opraven a pro ty, kterých se chyba týká, můžete ručně stáhnout nejnovější verzi Průzkumníka úložiště [zde](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště nepodporuje účty Služby ADFS.
* Klávesové zkratky pro "View Explorer" a "View Account Management" by měly být Ctrl/Cmd+Shift+E a Ctrl/Cmd+Shift+A.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Verze 0.9.1 a 0.9.0
10/20/2017
### <a name="new"></a>Nová
* Podpora preview pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytváření databází a kolekcí
    * Manipulace s daty
    * Dotazování, vytváření nebo odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelem definovaných funkcí nebo aktivačních událostí
    * Připojení k databázím a správa databází pomocí připojovacích řetězců
* Vylepšen výkon nahrávání/stahování mnoha malých objektů BLOB.
* Přidána akce "Opakovat vše", pokud jsou ve skupině pro odesílání objektů blob nebo skupině pro stahování objektů blob chyby.
* Průzkumník úložiště teď pozastaví iteraci během nahrávání a stahování objektů blob, pokud zjistí, že došlo ke ztrátě síťového připojení. Po obnovení síťového připojení pak můžete pokračovat v iteraci.
* Přidána možnost "Zavřít vše", "Zavřít ostatní", a "Zavřít" karty přes kontextové menu.
* Průzkumník úložiště teď používá nativní dialogy a nativní místní nabídky.
* Průzkumník úložiště je teď přístupnější. Mezi vylepšení patří:
    * Vylepšená podpora čtečky obrazovky pro NVDA ve Windows a pro VoiceOver na Macu
    * Vylepšené téma s vysokým kontrastem
    * Opravy tabulátorů a fokusu klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt blob s neplatným názvem souboru systému Windows, operace by se nezdaří. Průzkumník úložiště teď zjistí, jestli je název objektu blob neplatný, a zeptá se, jestli ho chcete buď zakódovat, nebo přeskočit objekt blob. Průzkumník úložiště také zjistí, zda se název souboru zdá být zakódován, a zeptá se vás, zda jej chcete před nahráním dekódovat.
* Během nahrávání objektů blob by se někdy editor cílového kontejneru objektů blob neaktualizoval správně. Tento problém byl opraven.
* Podpora pro několik forem připojovacích řetězců a identifikátorů URI SAS se vrátila. Vyřešili jsme všechny známé problémy, ale pokud narazíte na další problémy, pošlete nám prosím zpětnou vazbu.
* Oznámení o aktualizaci bylo přerušeno pro některé uživatele v 0.9.0. Tento problém byl opraven a pro osoby ovlivněné chybou můžete ručně stáhnout nejnovější verzi průzkumníka úložiště [zde](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Známé problémy
* Průzkumník úložiště nepodporuje účty Služby ADFS.
* Klávesové zkratky pro "View Explorer" a "View Account Management" by měly být Ctrl/Cmd+Shift+E a Ctrl/Cmd+Shift+A.
* Při cílení na Azure Stack, nahrávání určitých souborů jako připojit objekty BLOB může selhat.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Je to proto, že používáme zrušit filtr řešení zde popsané.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Prostředí Electron používané aplikací Storage Explorer má potíže s hardwarovou akcelerací některých GPU (grafických procesorů). Pokud Průzkumník úložiště zobrazuje prázdné (prázdné) hlavní okno, můžete zkusit spustit Průzkumníka úložiště z příkazového `--disable-gpu` řádku a zakázat akceleraci GPU přidáním přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Verze 0.8.16
8/21/2017

### <a name="new"></a>Nová
* Když otevřete objekt blob, Průzkumník úložiště vás vyzve k nahrání staženého souboru, pokud je zjištěna změna
* Vylepšené prostředí pro přihlášení do azure stacku
* Vylepšen výkon nahrávání/stahování mnoha malých souborů současně


### <a name="fixes"></a>Opravy
* U některých typů objektů blob by někdy mělo za následek restartování nahrávání možnost "nahradit" během konfliktu nahrávání.
* Ve verzi 0.8.15 by se nahrávání někdy zastavilo na 99%.
* Při nahrávání souborů do sdílené složky, pokud jste se rozhodli nahrát do adresáře, který ještě neexistoval, nahrávání by se nezdaří.
* Průzkumník úložiště nesprávně generoval časová razítka pro sdílené přístupové podpisy a dotazy na tabulky.


### <a name="known-issues"></a>Známé problémy
* Použití připojovacího řetězce názvu a klíče v současné době nefunguje. Bude opravena v příští verzi. Do té doby můžete použít připojit s názvem a klíčem.
* Pokud se pokusíte otevřít soubor s neplatným názvem souboru systému Windows, bude stahování mít za následek chybu souboru, který nebyl nalezen.
* Po kliknutí na tlačítko "Zrušit" u úkolu může chvíli trvat, než se tento úkol zruší. Toto je omezení knihovny uzlů úložiště Azure.
* Po dokončení nahrávání objektu blob se aktualizuje karta, která nahrávání iniciovala. Jedná se o změnu oproti předchozímu chování a také způsobí, že budete převést zpět do kořenového adresáře kontejneru, ve které se nacházíte.
* Pokud zvolíte nesprávný certifikát PIN/Smartcard, budete muset restartovat, aby Průzkumník úložiště toto rozhodnutí zapomněl.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje pro filtrování odběrů.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pro uživatele na Ubuntu 14.04, budete muset zajistit GCC je up-to-date - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17.04, budete muset nainstalovat GConf - to lze provést spuštěním následujících příkazů, a pak restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Verze 0.8.14
06/22/2017

### <a name="new"></a>Nová

* Byla aktualizována verze Electron na verzi 1.7.2, aby bylo možné využít několik důležitých aktualizací zabezpečení
* Nyní můžete rychle přistupovat k průvodci odstraňováním potíží online z nabídky nápovědy
* [Průvodce][2] odstraňováním potíží průzkumníka úložiště
* [Pokyny pro][3] připojení k předplatnému Azure Stacku

### <a name="known-issues"></a>Známé problémy

* Tlačítka v dialogovém okně potvrzení odstranit složku se nezaregistrují kliknutím myši na Linux. obejít je použít klávesu Enter
* Pokud zvolíte nesprávný certifikát PIN/ Smartcard, budete muset restartovat, aby Průzkumník úložiště zapomněl rozhodnutí
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby
* Panel Nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje, abyste mohli filtrovat odběry.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Ubuntu 14.04 instalace potřebuje gcc verzi aktualizována nebo upgradována - kroky k upgradu jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Verze 0.8.13
05/12/2017

#### <a name="new"></a>Nová

* [Průvodce][2] odstraňováním potíží průzkumníka úložiště
* [Pokyny pro][3] připojení k předplatnému Azure Stacku

#### <a name="fixes"></a>Opravy

* Opraveno: Nahrávání souborů mělo vysokou šanci způsobit chybu nedostatku paměti
* Oprava: Nyní se můžete přihlásit pomocí KÓDU PIN/Smartcard
* Oprava: Open in Portal teď funguje s Azure China 21Vianet, Azure Germany, Azure US Government a Azure Stack
* Opraveno: Při nahrávání složky do kontejneru objektů blob se někdy vyskytla chyba "Neplatná operace"
* Opraveno: Při správě snímků bylo zakázáno vybrat vše, co bylo zakázáno.
* Oprava: Metadata základního objektu blob mohou být přepsána po zobrazení vlastností jeho snímků.

#### <a name="known-issues"></a>Známé problémy

* Pokud zvolíte nesprávný certifikát PIN/ Smartcard, budete muset restartovat, aby Průzkumník úložiště zapomněl rozhodnutí
* Při přiblížení nebo oddálení může úroveň přiblížení na okamžik obnovit výchozí úroveň
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby
* Panel Nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje, abyste mohli filtrovat odběry.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Ubuntu 14.04 instalace potřebuje gcc verzi aktualizována nebo upgradována - kroky k upgradu jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Verze 0.8.12 a 0.8.11 a 0.8.10
04/07/2017

#### <a name="new"></a>Nová

* Průzkumník úložiště se nyní automaticky zavře při instalaci aktualizace z oznámení o aktualizaci
* Rychlý přístup na místě poskytuje vylepšené prostředí pro práci s často používanými prostředky
* V editoru kontejnerů objektů blob teď můžete zjistit, ke kterému virtuálnímu počítači patří pronajatý objekt blob
* Nyní můžete sbalit levý boční panel
* Zjišťování nyní běží ve stejnou dobu jako stahování
* Použití statistiky v editorech kontejnerů objektů blob, sdílení souborů a tabulek k zobrazení velikosti prostředku nebo výběru
* Teď se můžete přihlásit k účtům Azure Active Directory (AAD) založených na Azure Stacku.
* Nyní můžete nahrát archivní soubory přes 32 MB na účty úložiště Premium
* Vylepšená podpora usnadnění
* Nyní můžete přidat důvěryhodné certifikáty X.509 TLS/SSL kódované base-64 pomocí příkazu Upravit -&gt; SSL Certificates -&gt; Import Certificates

#### <a name="fixes"></a>Opravy

* Opraveno: po aktualizaci přihlašovacích údajů účtu by se stromové zobrazení někdy automaticky neaktualizovalo
* Opraveno: generování SAS pro fronty a tabulky emulátorů by mělo za následek neplatnou adresu URL
* Opraveno: účty úložiště premium lze nyní rozšířit, když je povolen proxy server
* Opraveno: tlačítko Použít na stránce správy účtů by nefungovalo, pokud byste měli vybrané 1 nebo 0 účtů
* Oprava: nahrávání objektů BLOB, které vyžadují řešení konfliktů, může selhat – opraveno v 0.8.11
* Opraveno: odeslání zpětné vazby bylo přerušeno v 0.8.11 - opraveno v 0.8.12

#### <a name="known-issues"></a>Známé problémy

* Po upgradu na 0.8.10 budete muset aktualizovat všechna pověření.
* Při přiblížení nebo oddálení může úroveň přiblížení na okamžik obnovit výchozí úroveň.
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby.
* Panel nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje, abyste mohli filtrovat odběry.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování.
* Přestože Azure Stack aktuálně nepodporuje sdílené složky souborů, uzel sdílené složky se stále zobrazuje pod připojeným účtem úložiště Azure Stack.
* Ubuntu 14.04 instalace potřebuje gcc verzi aktualizována nebo upgradována - kroky k upgradu jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Verze 0.8.9 a 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nová

* Průzkumník úložiště 0.8.9 automaticky stáhne nejnovější verzi aktualizací.
* Oprava hotfix: použití portálu generovaného identifikátoru URI s rozhraním SAS k připojení účtu úložiště by mělo za následek chybu.
* Teď můžete vytvářet, spravovat a propagovat snímky objektů blob.
* Teď se můžete přihlásit k účtům Azure China 21Vianet, Azure Germany a Azure US Government.
* Nyní můžete změnit úroveň přiblížení. Pomocí voleb v nabídce Zobrazení můžete přiblížit, oddálit a obnovit přiblížení.
* Znaky Unicode jsou nyní podporovány v metadatech uživatelů pro objekty BLOB a soubory.
* Vylepšení přístupnosti.
* Poznámky k verzi další verze lze zobrazit z oznámení aktualizace. Aktuální poznámky k verzi můžete také zobrazit z nabídky Nápověda.

#### <a name="fixes"></a>Opravy

* Opraveno: číslo verze je nyní správně zobrazeno v Ovládacích panelech v systému Windows
* Opraveno: vyhledávání již není omezeno na 50 000 uzlů
* Opraveno: nahrát na sdílené složky točil navždy, pokud cílový adresář ještě neexistoval
* Oprava: vylepšená stabilita při dlouhých nahráváních a stahování

#### <a name="known-issues"></a>Známé problémy

* Při přiblížení nebo oddálení může úroveň přiblížení na okamžik obnovit výchozí úroveň.
* Rychlý přístup funguje pouze s položkami založenými na předplatném. Místní prostředky nebo prostředky připojené prostřednictvím klíče nebo tokenu SAS nejsou v této verzi podporovány.
* V závislosti na počtu prostředků, které máte, může přechod na cílový prostředek trvat několik sekund.
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby.

12/16/2016
### <a name="version-087"></a>Verze 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nová

* Můžete zvolit způsob řešení konfliktů na začátku aktualizace, stažení nebo kopírování relace v okně Aktivity.
* Najeďte na kartu a zoněkolikáte celou cestu k prostředku úložiště.
* Když kliknete na kartu, synchronizuje se s jeho umístěním v levém navigačním podokně

#### <a name="fixes"></a>Opravy

* Opraveno: Průzkumník úložiště je nyní důvěryhodná aplikace na Macu
* Opraveno: Ubuntu 14.04 je opět podporován
* Opraveno: Někdy při načítání odběrů bliká ujzlíku účtu.
* Opraveno: Někdy nebyly v navigačním podokně na levé straně uvedeny všechny prostředky úložiště.
* Opraveno: Podokno akcí se někdy zobrazuje prázdné akce
* Opraveno: Velikost okna z poslední uzavřené relace je nyní zachována
* Opraveno: Pomocí kontextové nabídky můžete otevřít více karet pro stejný zdroj.

#### <a name="known-issues"></a>Známé problémy

* Rychlý přístup funguje pouze s položkami založenými na předplatném. Místní prostředky nebo prostředky připojené pomocí klíče nebo tokenu SAS nejsou v této verzi podporovány.
* Může trvat několik sekund, než se rychlý přístup přejde k cílovému prostředku, v závislosti na tom, kolik prostředků máte
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby
* Hledání zpracovává hledání v rámci zhruba 50 000 uzlů – po tomto, výkon může být ovlivněna nebo může způsobit neošetřené výjimky
* Při prvním použití Průzkumníka úložiště v systému macOS se může zobrazit několik výzev s žádostí o oprávnění uživatele k přístupu ke klíčence. Doporučujeme vybrat možnost Vždy povolit, aby se výzva znovu nezobrazovala.

11/18/2016
### <a name="version-086"></a>Verze 0.8.6

#### <a name="new"></a>Nová

* Nyní můžete připnout nejčastěji používané služby do rychlého přístupu pro snadnou navigaci
* Nyní můžete otevřít více editorů na různých kartách. Jedním kliknutím otevřete dočasnou kartu; dvojitým kliknutím otevřete trvalou kartu. Můžete také kliknout na dočasnou kartu, aby byla trvalá karta
* Provedli jsme znatelné vylepšení výkonu a stability pro nahrávání a stahování, zejména pro velké soubory na rychlých strojích
* Prázdné "virtuální" složky lze nyní vytvořit v kontejnerech objektů blob
* Znovu jsme zavedli vyhledávání s rozsahem s naším novým rozšířeným vyhledáváním podřetězců, takže nyní máte dvě možnosti vyhledávání:
    * Globální vyhledávání - stačí zadat hledaný výraz do textového pole vyhledávání
    * Hledání s rozsahem – klikněte na ikonu lupy vedle uzlu, přidejte hledaný výraz na konec cesty nebo klikněte pravým tlačítkem myši a vyberte "Hledat odtud"
* Přidali jsme různé motivy: Light (výchozí), Tmavý, Vysoký kontrast černá a Vysoká kontrastní bílá. Změna předvolby motivů přejdete na Upravit –&gt; motivy.
* Můžete upravit vlastnosti objektů Blob a souborů.
* Nyní podporujeme kódované (base64) a nekódované zprávy fronty
* Na Linuxu je nyní vyžadován 64bitový operační systém. Pro tuto verzi podporujeme pouze 64bitové Ubuntu 16.04.1 LTS
* Aktualizovali jsme naše logo!

#### <a name="fixes"></a>Opravy

* Opraveno: Problémy se zmrazením obrazovky
* Oprava: Vylepšené zabezpečení
* Opraveno: Někdy se mohou objevit duplicitní připojené účty
* Oprava: Objekt blob s nedefinovaným typem obsahu může generovat výjimku
* Opraveno: Otevření panelu dotazů v prázdné tabulce nebylo možné
* Opraveno: Ve vyhledávání se liší chyby
* Opraveno: Zvýšil počet načtených prostředků z 50 na 100 při kliknutí na tlačítko "Načíst více"
* Opraveno: Při prvním spuštění, pokud je účet přihlášen, nyní ve výchozím nastavení vybereme všechna předplatná pro tento účet

#### <a name="known-issues"></a>Známé problémy

* Tato verze Průzkumníka úložiště neběží na Ubuntu 14.04
* Chcete-li otevřít více karet pro stejný zdroj, neklikejte nepřetržitě na stejný zdroj. Klikněte na jiný zdroj a pak se vrátit a pak klikněte na původní zdroj otevřít znovu v jiné záložce
* Rychlý přístup funguje pouze s položkami založenými na předplatném. Místní prostředky nebo prostředky připojené pomocí klíče nebo tokenu SAS nejsou v této verzi podporovány.
* Může trvat několik sekund, než se rychlý přístup přejde k cílovému prostředku, v závislosti na tom, kolik prostředků máte
* S více než 3 skupiny objektů BLOB nebo soubory nahrávání ve stejnou dobu může způsobit chyby
* Hledání zpracovává hledání v rámci zhruba 50 000 uzlů – po tomto, výkon může být ovlivněna nebo může způsobit neošetřené výjimky

03/10/2016
### <a name="version-085"></a>Verze 0.8.5

#### <a name="new"></a>Nová

* Nyní můžete použít portal-generované Klíče SAS připojit k účty úložiště a prostředky

#### <a name="fixes"></a>Opravy

* Opraveno: spor během vyhledávání někdy způsobil, že se uzly staly nerozšiřitelnými
* Oprava: "Použití protokolu HTTP" nefunguje při připojování k účtům úložiště s názvem účtu a klíčem
* Opraveno: Klíče SAS (speciálně portálem generované) vrátí chybu "koncového lomítka"
* Opraveno: problémy s importem tabulky
    * Někdy klíč oddílu a klíč řádku byly stornovány
    * Nelze číst "nulové" klíče oddílu

#### <a name="known-issues"></a>Známé problémy

* Hledání zpracovává vyhledávání v rámci zhruba 50 000 uzlů – poté může být ovlivněn výkon
* Azure Stack aktuálně nepodporuje soubory, takže při pokusu o rozbalení souborů se zobrazí chyba

09/12/2016
### <a name="version-084"></a>Verze 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nová

* Generování přímých odkazů na účty úložiště, kontejnery, fronty, tabulky nebo sdílené složky pro sdílení a snadný přístup k vašim prostředkům – podpora windows a mac os
* Hledání kontejnerů objektů blob, tabulek, front, sdílených složek nebo účtů úložiště z vyhledávacího pole
* Nyní můžete seskupit klauzule v tvůrce dotazů na tabulky.
* Přejmenování a kopírování a vkládání kontejnerů objektů BLOB, sdílených složek, tabulek, objektů BLOB, složek objektů blob, souborů a adresářů z účtů a kontejnerů připojených k SAS
* Přejmenování a kopírování kontejnerů objektů blob a sdílených složek nyní zachová vlastnosti a metadata

#### <a name="fixes"></a>Opravy

* Opraveno: Nelze upravovat entity tabulky, pokud obsahují logické nebo binární vlastnosti.

#### <a name="known-issues"></a>Známé problémy

* Hledání zpracovává vyhledávání v rámci zhruba 50 000 uzlů – poté může být ovlivněn výkon

08/03/2016
### <a name="version-083"></a>Verze 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nová

* Přejmenování kontejnerů, tabulek, sdílených složek
* Vylepšené prostředí tvůrce dotazů
* Možnost ukládání a načítání dotazů
* Přímé odkazy na účty úložiště nebo kontejnery, fronty, tabulky nebo sdílené složky pro sdílení a snadný přístup k prostředkům (pouze Windows - podpora macOS brzy!)
* Schopnost spravovat a konfigurovat pravidla CORS

#### <a name="fixes"></a>Opravy

* Oprava: Účty Microsoft vyžadují opětovné ověření každých 8 až 12 hodin.

#### <a name="known-issues"></a>Známé problémy

* Někdy se může zobrazit zamrzlé uj.
* Instalace macOS může vyžadovat zvýšená oprávnění
* Panel Nastavení účtu může ukázat, že je třeba znovu zadat přihlašovací údaje, abyste mohli filtrovat odběry.
* Přejmenování sdílených složek, kontejnerů objektů blob a tabulek nezachová metadata ani jiné vlastnosti kontejneru, jako je kvóta sdílení souborů, úroveň veřejného přístupu nebo zásady přístupu
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenované kontejneru objektů blob) nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty BLOB, soubory a entity jsou zachovány během přejmenování
* Kopírování nebo přejmenování prostředků nefunguje v rámci účtů připojených k SAS

07/07/2016
### <a name="version-082"></a>Verze 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nová

* Účty úložiště jsou seskupeny podle předplatných. úložiště a prostředky připojené pomocí klíče nebo SAS jsou zobrazeny pod uzlem (Místní a připojené)
* Odhlášení z účtů v panelu Nastavení účtu Azure
* Konfigurace nastavení proxy serveru pro povolení a správu přihlášení
* Vytvoření a přerušení zapůjčení objektů blob
* Otevření kontejnerů objektů blob, front, tabulek a souborů jediným kliknutím

#### <a name="fixes"></a>Opravy

* Opraveno: zprávy fronty vložené pomocí knihoven .NET nebo Java nejsou správně dekódovány ze základní 64
* Oprava: $metrics tabulky se nezobrazují pro účty úložiště objektů Blob.
* Opraveno: uzel tabulek nefunguje pro místní (vývojové) úložiště

#### <a name="known-issues"></a>Známé problémy

* Instalace macOS může vyžadovat zvýšená oprávnění

06/15/2016
### <a name="version-080"></a>Verze 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nová

* Podpora sdílení souborů: prohlížení, nahrávání, stahování, kopírování souborů a adresářů, identifikátory URI SAS (vytváření a připojování)
* Vylepšené uživatelské prostředí pro připojení k úložišti pomocí identifikátorů URI s as nebo klíčů účtů
* Exportovat výsledky dotazu na tabulku
* Změna pořadí a přizpůsobení sloupce tabulky
* Zobrazení $logs kontejnerů objektů blob a $metrics tabulek pro účty úložiště s povolenými metrikami
* Vylepšené chování exportu a importu, nyní zahrnuje typ hodnoty vlastnosti

#### <a name="fixes"></a>Opravy

* Opraveno: nahrávání nebo stahování velkých objektů BLOB může vést k neúplným nahráváním/stahování
* Opraveno: úpravy, přidání nebo import entity s číselnou hodnotou řetězce ("1") ji převedou na
* Oprava: Nelze rozbalit uzel tabulky v prostředí místního rozvoje.

#### <a name="known-issues"></a>Známé problémy

* $metrics tabulky nejsou viditelné pro účty úložiště objektů Blob
* Zprávy fronty přidané programově nemusí být zobrazeny správně, pokud jsou zprávy kódovány pomocí kódování Base64

05/17/2016
### <a name="version-07201605090"></a>Verze 0.7.20160509.0

#### <a name="new"></a>Nová

* Lepší zpracování chyb při selhání aplikace

#### <a name="fixes"></a>Opravy

* Opravena chyba, kdy se zprávy informačního panelu někdy nezobrazovala, když byly vyžadovány přihlašovací údaje

#### <a name="known-issues"></a>Známé problémy

* Tabulky: Přidání, úpravy nebo import entity, která má vlastnost s nejednoznačně číselnou hodnotou, například "1" nebo "1.0", a uživatel se pokusí odeslat jako `Edm.String`, hodnota se vrátí prostřednictvím klientského rozhraní API jako Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Verze 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nová

* Podpora tabulky: zobrazení, dotazování, export, import a CRUD operace pro entity
* Podpora fronty: zobrazení, přidávání, dequeueing zpráv
* Generování identifikátorů URI SAS pro účty úložiště
* Připojení k účtům úložiště pomocí identifikátorů URI SAS
* Aktualizace oznámení pro budoucí aktualizace Průzkumníka úložiště
* Aktualizovaný vzhled a chování

#### <a name="fixes"></a>Opravy

* Vylepšení výkonu a spolehlivosti

### <a name="known-issues-amp-mitigations"></a>Známé &amp; skutečnosti snižující závažnost rizika

* Stažení velkých blob souborů nefunguje správně - doporučujeme používat AzCopy, zatímco budeme řešit tento problém
* Pověření účtu nebudou načtena ani uložena do mezipaměti, pokud domovskou složku nelze najít nebo do ní nelze zapsat.
* Pokud přidáváme, upravujeme nebo importujeme entitu, která má vlastnost s nejednoznačně číselnou hodnotou, například "1" `Edm.String`nebo "1.0", a uživatel se pokusí odeslat jako , hodnota se vrátí prostřednictvím klientského rozhraní API jako Edm.Double
* Při importu souborů CSV s víceřádkovými záznamy se data mohou

02/03/2016

### <a name="version-07201601291"></a>Verze 0.7.20160129.1

#### <a name="fixes"></a>Opravy

* Lepší celkový výkon při nahrávání, stahování a kopírování objektů BLOB

01/14/2016

### <a name="version-07201601050"></a>Verze 0.7.20160105.0

#### <a name="new"></a>Nová

* Podpora Linuxu (paritní funkce pro OSX)
* Přidání kontejnerů objektů blob s klíčem Sdílené přístupové podpisy (SAS)
* Přidání účtů úložiště pro Azure China 21Vianet
* Přidání účtů úložiště s vlastními koncovými body
* Otevření a zobrazení objektů BLOB obsahu textu a obrázku
* Zobrazení a úprava vlastností objektu blob a metadat

#### <a name="fixes"></a>Opravy

* Opraveno: nahrání nebo stažení velkého počtu objektů BLOB (500+) může někdy způsobit, že aplikace bude mít bílou obrazovku
* Oprava: Při nastavování úrovně veřejného přístupu kontejneru blob se nová hodnota neaktualizuje, dokud znovu nenastavíte fokus na kontejner. Dialogové okno také vždy výchozí "Žádný veřejný přístup", a nikoli aktuální aktuální hodnotu.
* Lepší celková podpora klávesnice/přístupnosti a uj.
* Popis cesty text historie zalomí, když je dlouhý s bílým prostorem
* Dialogové okno SAS podporuje ověření vstupu
* Místní úložiště je stále k dispozici i v případě, že vypršela platnost pověření uživatele.
* Při odstranění otevřeného kontejneru objektů blob je průzkumník objektů blob na pravé straně zavřený

#### <a name="known-issues"></a>Známé problémy

* Linux instalace potřebuje gcc verze aktualizována nebo upgradována - kroky k upgradu jsou níže:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Verze 0.7.20151116.0

#### <a name="new"></a>Nová

* macOS a verze Windows
* Přihlaste se k zobrazení účtů úložiště – použijte svůj účet Org, účet Microsoft, 2FA atd.
* Úložiště pro místní vývoj (použijte emulátor úložiště, jenom windows)
* Azure Resource Manager a klasická podpora prostředků
* Vytvoření a odstranění objektů BLOB, front nebo tabulek
* Hledání konkrétních objektů BLOB, front nebo tabulek
* Prozkoumejte obsah kontejnerů objektů blob
* Zobrazení a procházení adresářů
* Nahrávání, stahování a odstraňování objektů BLOB a složek
* Zobrazení a úprava vlastností objektu blob a metadat
* Generovat klíče SAS
* Správa a vytvoření uložených přístupových zásad (SAP)
* Hledání objektů BLOB podle předpony
* Přetažení souborů pro nahrávání nebo stahování

#### <a name="known-issues"></a>Známé problémy

* Při nastavování úrovně veřejného přístupu kontejneru objektů blob se nová hodnota neaktualizuje, dokud znovu nenastavíte fokus na kontejneru.
* Když otevřete dialogové okno pro nastavení úrovně veřejného přístupu, zobrazí se vždy jako výchozí "Žádný veřejný přístup" a nikoli skutečná aktuální hodnota.
* Stažené objekty BLOB nelze přejmenovat.
* Položky protokolu aktivit se někdy "zaseknou" ve stavu probíhajícího, když dojde k chybě a chyba se nezobrazí
* Někdy se při pokusu o nahrání nebo stažení velkého počtu objektů BLOB zhroutí nebo zcela zbělá.
* Někdy zrušení operace kopírování nefunguje
* Při vytváření kontejneru (objekt blob/fronta/tabulka), pokud zadáte neplatný název a budete pokračovat v vytváření jiného pod jiným typem kontejneru, nelze nastavit fokus na nový typ
* Nelze vytvořit novou složku nebo přejmenovat složku




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
