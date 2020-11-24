---
title: Zpráva k vydání verze Průzkumníka služby Microsoft Azure Storage
description: Přečtěte si poznámky k verzi pro nejnovější verzi Průzkumník služby Microsoft Azure Storage. Zobrazují se také poznámky k verzi pro předchozí verze.
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
ms.openlocfilehash: a53f947eb2a44cc8773be8ee2b2bd03ca899be22
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95520998"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Zpráva k vydání verze Průzkumníka služby Microsoft Azure Storage

Tento článek obsahuje nejnovější poznámky k verzi pro Průzkumník služby Azure Storage a také poznámky k verzi pro předchozí verze. 

[Průzkumník služby Microsoft Azure Storage](./vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, MacOS a Linux.

Pokud si chcete stáhnout předchozí verze Průzkumník služby Storage, můžete navštívit [stránku vydání](https://github.com/microsoft/AzureStorageExplorer/releases) našeho úložiště GitHub.

## <a name="version-1110"></a>1.11.0 verze
11/4/2019

### <a name="new"></a>Nová
* Operace pro objekty blob, ADLS Gen2 a Managed Disks používají integrovaný AzCopy. Přesněji řečeno, následující operace se provádějí pomocí AzCopy:
   * Objekty blob
      * Otevřené pro úpravy a nahrávání
      * Nahrávání, včetně přetažení &
      * Stáhnout
      * Kopírovat & vkládání #1249
      * Odstranit
   * Objekty blob ADLS Gen2
      * Nahrávání, včetně přetažení &
      * Stáhnout
      * Kopírovat & vložení
      * Odstranit, včetně odstranění složky
   * Spravované disky
      * Nahrávání
      * Stáhnout
      * Kopírovat & vložení

   Kromě toho se do integrovaného prostředí AzCopy přidalo několik často požadovaných funkcí:
   * Řešení konfliktů – během přenosu budete vyzváni k vyřešení konfliktů. #1455
   * Nahrát jako objekty blob stránky – můžete zvolit, jestli AzCopy soubory. VHD a. vhdx nahrávají jako objekty blob stránky. #1164 a #1601
   * Konfigurovatelné parametry AzCopy – k vyladění výkonu a využití prostředků AzCopy se přidalo několik nastavení. Další podrobnosti najdete níže.

* Pro povolení přístupu k více protokolům ADLS Gen2 a blobů a další vylepšení ADLS Gen2 prostředí jsme pro účty ADLS Gen2 přidali následující funkce:
   * Hledání s použitím popisných názvů k nastavení oprávnění seznamu ACL
   * Zobrazit skryté kontejnery, například $logs a $web
   * Získání a přerušení zapůjčení kontejneru
   * Získat a poškodit #848 zapůjčení objektů BLOB
   * Správa zásad přístupu kontejneru
   * Konfigurace úrovní přístupu objektů BLOB
   * Kopírovat & vkládání objektů BLOB

* V této verzi jsme si předcházejí 17 dalších jazyků. Můžete přepnout na konkrétní jazyk na stránce nastavení v části "aplikace" → "místní nastavení" → "Language (Preview)". Pořád pracujeme na překladu dalších řetězců a vylepšení kvality překladu. Pokud máte nějakou zpětnou vazbu týkající se překladu, nebo pokud si všimnete řetězce, který ještě není přeložený, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* V každé vydané verzi se snažíme připojit několik nastavení, aby bylo možné vypínat Průzkumník služby Storage. V této verzi jsme přidali nastavení pro další konfiguraci AzCopy a také pro skrytí uzlů služby:
   * Limit šířky pásma AzCopy – pomáhá řídit, jak velká část sítě AzCopy používá. Toto nastavení najdete v části "přenos" → "AzCopy" → "maximální přenosová rychlost". #1099
   * AzCopy MD5 check – umožňuje nakonfigurovat, jestli a jak čistě AzCopy kontroluje hodnoty hash MD5 při stažení. Toto nastavení najdete v části "přenos" → "AzCopy" → "ověření MD5".
   * AzCopy souběžnost a velikost vyrovnávací paměti – ve výchozím nastavení AzCopy analyzuje počítač a určí tak přiměřené výchozí hodnoty pro tato nastavení. Pokud ale dojde k problémům s výkonem, můžete tato pokročilá nastavení použít k dalšímu přizpůsobení toho, jak AzCopy běží na vašem počítači. Tato nastavení najdete v části "přenosy" → "AzCopy". #994
   * Zobrazení a skrytí uzlů služby – Tato nastavení poskytují možnosti pro zobrazení nebo skrytí kterékoli ze služeb Azure, které Průzkumník služby Storage podporuje. Tato nastavení najdete v části "služby". #1877

* Při vytváření snímku spravovaného disku je teď k dispozici výchozí název. #1847
* Při připojení ke službě Azure AD, pokud připojíte ADLS Gen2 kontejner objektů blob, zobrazí se vedle uzlu "(ADLS Gen2)". #1861

### <a name="fixes"></a>Opravy
* Při kopírování, nahrávání nebo stahování velkých disků se Průzkumník služby Storage někdy nepodaří odvolat přístup k diskům zapojeným do operace. To jsme opravili. #2048
* Při zobrazení dotazu na klíč oddílu došlo k chybě statistiky tabulky. To jsme opravili. #1886

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage 1.11.0 nyní vyžaduje koncový bod DFS (například "myaccount.dfs.core.windows.net") pro připojení k kontejnerům ADLS Gen2. Předchozí verze Průzkumník služby Storage vám umožňují použít koncový bod objektu BLOB. Tyto přílohy po upgradu na 1.11.0 už nemusí fungovat. Pokud se setkáte s tímto problémem, znovu se připojte pomocí koncového bodu DFS.
* Číselná nastavení nejsou kontrolována bez ohledu na to, zda leží v platném rozsahu. #2140
* Kopírování kontejnerů objektů BLOB z jednoho účtu úložiště do jiného ve stromovém zobrazení může selhat. Chystáme se problém prozkoumat. #2124
* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="previous-releases"></a>Předchozí verze

* [1.10.1 verze](#version-1101)
* [1.10.0 verze](#version-1100)
* [1.9.0 verze](#version-190)
* [1.8.1 verze](#version-181)
* [1.8.0 verze](#version-180)
* [Verze 1.7.0](#version-170)
* [1.6.2 verze](#version-162)
* [1.6.1 verze](#version-161)
* [1.6.0 verze](#version-160)
* [1.5.0 verze](#version-150)
* [1.4.4 verze](#version-144)
* [1.4.3 verze](#version-143)
* [1.4.2 verze](#version-142)
* [Verze 1.4.1](#version-141)
* [1.3.0 verze](#version-130)
* [1.2.0 verze](#version-120)
* [1.1.0 verze](#version-110)
* [1.0.0 verze](#version-100)
* [0.9.6 verze](#version-096)
* [0.9.5 verze](#version-095)
* [Verze 0.9.4 a 0.9.3](#version-094-and-093)
* [0.9.2 verze](#version-092)
* [Verze 0.9.1 a 0.9.0](#version-091-and-090)
* [0.8.16 verze](#version-0816)
* [0.8.14 verze](#version-0814)
* [0.8.13 verze](#version-0813)
* [Verze 0.8.12 a 0.8.11 a 0.8.10](#version-0812-and-0811-and-0810)
* [Verze 0.8.9 a 0.8.8](#version-089-and-088)
* [0.8.7 verze](#version-087)
* [0.8.6 verze](#version-086)
* [0.8.5 verze](#version-085)
* [0.8.4 verze](#version-084)
* [0.8.3 verze](#version-083)
* [0.8.2 verze](#version-082)
* [0.8.0 verze](#version-080)
* [0.7.20160509.0 verze](#version-07201605090)
* [0.7.20160325.0 verze](#version-07201603250)
* [0.7.20160129.1 verze](#version-07201601291)
* [0.7.20160105.0 verze](#version-07201601050)
* [0.7.20151116.0 verze](#version-07201511160)

## <a name="version-1101"></a>1.10.1 verze
9/19/2019

### <a name="hotfix"></a>Instalaci
* Někteří uživatelé narazili na chybu v 1.10.0 při pokusu o zobrazení jejich dat ve svých účtech ADLS 1. generace. Tato chyba zabránila správnému vykreslení panelu Průzkumníka. To jsme opravili. #1853 #1865

### <a name="new"></a>Nová
* Průzkumník služby Storage má teď vyhrazené uživatelské rozhraní pro nastavení. Můžete k němu přistoupit buď z úpravy, nebo kliknutím na ikonu nastavení (ozubené kolo) na levém panelu nástrojů. Tato funkce je prvním krokem, který zabereme do poskytování nejrůznějších [nastavení požadovaných pro uživatele](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Od tohoto vydání jsou podporovaná následující nastavení:
  * Motiv
  * Proxy server
  * Odhlásit při ukončení #6
  * Povolit přihlášení ke toku kódu zařízení
  * Automaticky aktualizovat #1526
  * Povolit AzCopy
  * AzCopy (SAS): Pokud existují další nastavení, která byste chtěli přidat, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) s popisem nastavení, které chcete zobrazit.
* Průzkumník služby Storage teď podporuje Managed Disks. Můžete:
  * Nahrání místního virtuálního pevného disku na nový disk
  * Stažení disku
  * Kopírování a vkládání disků napříč skupinami prostředků a oblastmi
  * Odstranit disky
  * Vytvoření snímku disku

Nahrávání, stahování a kopírování disků v různých oblastech využívá AzCopy v10 za účelem.
* Průzkumník služby Storage se teď dají instalovat přes modul snap-in pro Linux. Při instalaci prostřednictvím obchodu s modulem snap-in jsou pro vás nainstalovány všechny závislosti včetně .NET Core! V současné době jsme ověřili, že Průzkumník služby Storage správně funguje na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z obchodu pro jiné Linux distribuce, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu s modulem snap-in najdete v naší [příručce Začínáme](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux). #68
* Byly provedeny dvě významné změny pro připojení k Azure Active Directory (Azure AD), které jsou určené k tomu, aby byla funkce užitečnější pro ADLS Gen2 uživatele:
  * Teď vyberte tenanta, ve kterém se prostředek, který připojujete. To znamená, že už nepotřebujete mít přístup ke službě RBAC v rámci předplatného prostředku.
  * Pokud připojujete ADLS Gen2 kontejner objektů blob, můžete se teď připojit k určité cestě v kontejneru.
* Při správě seznamů ACL pro ADLS Gen2 soubory a složky se teď Průzkumník služby Storage zobrazí popisné názvy entit v seznamu ACL. #957
* Když přidáváte identifikátor OID do seznamu ACL ADLS Gen2, Průzkumník služby Storage nyní ověří, že identifikátor OID patří platné entitě ve vašem tenantovi. #1603
* Klávesové zkratky pro navigaci mezi kartami teď používají kombinace standardních kláves. #1018
* Když kliknete na kartu, bude nyní zavřena. #1348
* Pokud přenos AzCopy obsahuje přeskočení a žádné chyby, Průzkumník služby Storage nyní zobrazí ikonu upozornění pro zvýraznění, že došlo k přeskočení. #1490
* Integrovaná AzCopy byla aktualizována na verzi 10.2.1. Navíc teď můžete zobrazit verzi AzCopy nainstalovanou v dialogovém okně o produktu. #1343

### <a name="fixes"></a>Opravy
* Mnoho uživatelů se spouštělo v různých verzích "nelze číst nedefinované verze" nebo "při práci s připojenými účty úložiště nejde načíst nedefinované připojení". I když pořád pokračujeme v šetření hlavní příčiny tohoto problému, Vylepšili jsme zpracování chyb při načítání připojených účtů úložiště. #1626, #985 a #1532
* Ve stromové struktuře Průzkumníka (levá strana) je možné přejít do stavu, ve kterém by fokus mohl opakovaně přejít na nejvyšší uzel. To jsme opravili. #1596
* Při správě snímků objektu BLOB by čtenáři obrazovky nečetli časové razítko přidružené ke snímku. To jsme opravili. #1202
* Nastavení proxy serveru na macOS nebylo nastaveno v čase, aby ho proces ověřování mohl použít. To jsme opravili. #1567
* Pokud byl účet úložiště v rámci svrchovaného cloudu připojený pomocí názvu a klíče, AzCopy nebude fungovat. To jsme opravili. #1544
* Při připojování pomocí připojovacího řetězce Průzkumník služby Storage nyní odstraní koncové mezery. #1387

### <a name="known-issues"></a>Známé problémy
* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.


## <a name="version-1100"></a>1.10.0 verze
9/12/2019

### <a name="new"></a>Nová

* Průzkumník služby Storage má teď vyhrazené uživatelské rozhraní pro nastavení. Můžete k němu přistoupit buď z úpravy, nebo kliknutím na ikonu nastavení (ozubené kolo) na levém panelu nástrojů. Tato funkce je prvním krokem, který zabereme do poskytování nejrůznějších [nastavení požadovaných pro uživatele](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Od tohoto vydání jsou podporovaná následující nastavení:
    * Motiv
    * Proxy server
    * Odhlásit při ukončení [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Povolit přihlášení ke toku kódu zařízení
    * Automaticky aktualizovat [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Povolit AzCopy
    * Doba trvání SAS AzCopy

    Pokud jsou k dispozici další nastavení, která byste chtěli přidat, [otevřete prosím problém na GitHubu s popisem nastavení, které chcete zobrazit](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Průzkumník služby Storage teď podporuje Managed Disks. Můžete:
    * Nahrání místního virtuálního pevného disku na nový disk
    * Stažení disku
    * Kopírování a vkládání disků napříč skupinami prostředků a oblastmi
    * Odstranit disky
    * Vytvoření snímku disku

    Nahrávání, stahování a kopírování disků v různých oblastech využívá AzCopy v10 za účelem.
* Průzkumník služby Storage se teď dají instalovat přes modul snap-in pro Linux. Při instalaci prostřednictvím obchodu s modulem snap-in jsou pro vás nainstalovány všechny závislosti včetně .NET Core! V současné době jsme ověřili, že Průzkumník služby Storage správně funguje na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z obchodu pro jiné Linux distribuce, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu s modulem snap-in najdete v naší [příručce Začínáme](./vs-azure-tools-storage-manage-with-storage-explorer.md). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Byly provedeny dvě významné změny pro připojení k Azure Active Directory (Azure AD), které jsou určené k tomu, aby byla funkce užitečnější pro ADLS Gen2 uživatele: * nyní vyberete tenanta, ve kterém je prostředek, který chcete připojit. To znamená, že už nepotřebujete mít přístup ke službě RBAC v rámci předplatného prostředku.
        * Pokud připojujete ADLS Gen2 kontejner objektů blob, můžete se teď připojit k určité cestě v kontejneru.
* Při správě seznamů ACL pro ADLS Gen2 soubory a složky se teď Průzkumník služby Storage zobrazí popisné názvy entit v seznamu ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Když přidáváte identifikátor OID do seznamu ACL ADLS Gen2, Průzkumník služby Storage nyní ověří, že identifikátor OID patří platné entitě ve vašem tenantovi. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Klávesové zkratky pro navigaci mezi kartami teď používají kombinace standardních kláves. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Když kliknete na kartu, bude nyní zavřena. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Pokud přenos AzCopy obsahuje přeskočení a žádné chyby, Průzkumník služby Storage nyní zobrazí ikonu upozornění pro zvýraznění, že došlo k přeskočení. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Integrovaná AzCopy byla aktualizována na verzi 10.2.1. Navíc teď můžete zobrazit verzi AzCopy nainstalovanou v dialogovém okně o produktu. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Opravy

* Mnoho uživatelů se spouštělo v různých verzích "nelze číst nedefinované verze" nebo "při práci s připojenými účty úložiště nejde načíst nedefinované připojení". I když pořád pokračujeme v šetření hlavní příčiny tohoto problému, Vylepšili jsme zpracování chyb při načítání připojených účtů úložiště. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)a [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Ve stromové struktuře Průzkumníka (levá strana) je možné přejít do stavu, ve kterém by fokus mohl opakovaně přejít na nejvyšší uzel. To jsme opravili. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Při správě snímků objektu BLOB by čtenáři obrazovky nečetli časové razítko přidružené ke snímku. To jsme opravili. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Nastavení proxy serveru na macOS nebylo nastaveno v čase, aby ho proces ověřování mohl použít. To jsme opravili. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Pokud byl účet úložiště v rámci svrchovaného cloudu připojený pomocí názvu a klíče, AzCopy nebude fungovat. To jsme opravili. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Při připojování pomocí připojovacího řetězce Průzkumník služby Storage nyní odstraní koncové mezery. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Známé problémy

* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-190"></a>1.9.0 verze
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Stáhnout Průzkumník služby Azure Storage 1.9.0
- [Průzkumník služby Azure Storage 1.9.0 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Průzkumník služby Azure Storage 1.9.0 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Průzkumník služby Azure Storage 1.9.0 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Kontejnery objektů BLOB teď můžete připojit přes Azure AD (oprávnění RBAC nebo ACL). Účelem této funkce je pomáhat uživatelům, kteří mají přístup k kontejnerům, ale nikoli k účtům úložiště, ve kterých jsou kontejnery. Další informace o této funkci najdete v našem průvodci Začínáme.
* Získání a přerušení zapůjčení teď funguje s RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Správa zásad přístupu a nastavení úrovně veřejného přístupu teď funguje s RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Odstraňování složek objektů BLOB teď funguje s RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Změna úrovně přístupu objektu BLOB teď funguje s RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Rychlý přístup teď můžete rychle obnovit prostřednictvím "" " [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funkce Preview

* Přihlášení toku kódu zařízení je teď dostupné ve verzi Preview. Pokud ho chcete povolit, přečtěte si "ve verzi Preview" → použít přihlášení ke toku kódu zařízení. Pro tuto funkci doporučujeme, aby všichni uživatelé, kteří mají potíže s prázdným systémem pro přihlášení, mohli tuto funkci vyzkoušet, protože se tak mohou považovat za spolehlivější způsob přihlašování.
* Průzkumník služby Storage integrovaná s AzCopy je aktuálně k dispozici ve verzi Preview. Pokud ho chcete povolit, přečtěte si "Preview" → "použití AzCopy pro lepší nahrávání a stahování objektů blob". Přenosy objektů BLOB dokončené s AzCopy by měly být rychlejší a více výkonné.

### <a name="fixes"></a>Opravy

* Opraveno nemůže načíst více než 50 předplatných pro jeden účet. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Opravili jsme, že tlačítko přihlásit nefunguje na informačním panelu, který se zobrazí, když se nezdařil přímý odkaz. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Nemusíte nahrávat soubory aplikace na macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Opravená možnost opakovat vše nefunguje pro přejmenování objektu blob, který selhal. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Opravená možnost zrušit nefunguje při otevírání objektu BLOB. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Opravili jsme několik problémů s pravopisem a popisy v celém produktu. Spousta díky všem, kdo nahlásil tyto problémy! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-181"></a>1.8.1 verze
5/13/2019

### <a name="hotfixes"></a>Opravy hotfix
* V některých případech nevrátí kliknutí na tlačítko načíst více na úrovni prostředků další stránku prostředků. To jsme opravili. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* V systému Windows by stažení AzCopy nebylo úspěšné, pokud se stáhl jeden soubor nebo složka a název souboru nebo složky měl znak, který byl pro cestu Windows neplatný. To jsme opravili. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* V extrémně vzácných případech, při provádění přejmenování sdílené složky nebo přejmenování ve sdílené složce, pokud se kopie pro přejmenování nepovedla nebo pokud se v úložišti nepovedlo potvrdit úspěch kopií s Azure, bylo by možné Průzkumník služby Storage odstranit původní soubory před dokončením kopírování. To jsme opravili.

### <a name="new"></a>Nová

* Integrovaná verze AzCopy byla aktualizována na verzi 10.1.0.
* Ctrl/Cmd + R se teď dají použít k aktualizaci aktuálně zaměřeného editoru. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Verze rozhraní API úložiště Azure Stack se změnila na 2017-04-17.
* Dialog spravovat přístup pro ADLS Gen2 teď zůstane maska synchronizovaná způsobem podobným ostatním nástrojům oprávnění POSIX. Uživatelské rozhraní vás také upozorní, pokud je provedena změna, která způsobí, že oprávnění uživatele nebo skupiny budou přesáhnout hranice masky. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Pro nahrávání AzCopy je teď povolený příznak pro výpočet a nastavení algoritmu hash MD5. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkce Preview

* Přihlášení toku kódu zařízení je teď dostupné ve verzi Preview. Pokud ho chcete povolit, přečtěte si "ve verzi Preview" → použít přihlášení ke toku kódu zařízení. Pro tuto funkci doporučujeme, aby všichni uživatelé, kteří mají potíže s prázdným systémem pro přihlášení, mohli tuto funkci vyzkoušet, protože se tak mohou považovat za spolehlivější způsob přihlašování.
* Průzkumník služby Storage integrovaná s AzCopy je aktuálně k dispozici ve verzi Preview. Pokud ho chcete povolit, přečtěte si "Preview" → "použití AzCopy pro lepší nahrávání a stahování objektů blob". Přenosy objektů BLOB dokončené s AzCopy by měly být rychlejší a více výkonné.

### <a name="fixes"></a>Opravy

* V dialogu zásady přístupu už nebude nastaveno datum vypršení platnosti u zásad přístupu k úložišti, které nemají vypršení platnosti. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* V dialogovém okně generovat SAS byly provedeny nějaké změny, aby se zajistilo, že uložené zásady přístupu se při generování SAS použijí správně. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Při pokusu o nahrání souboru zarovnanýho se systémem, který není 512 bajtů, do objektu blob stránky Průzkumník služby Storage nyní vystaví relevantnější chybu. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopírování kontejneru objektů blob, který využíval zobrazované jméno, by nedošlo k chybě. Nyní je použit skutečný název kontejneru objektů BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Pokus o provedení určitých akcí ve složce ADLS Gen2, která obsahovala znaky Unicode, by se nezdařil. Všechny akce by nyní měly fungovat. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-180"></a>1.8.0 verze
1. 5. 2019

### <a name="new"></a>Nová

* Integrovaná verze AzCopy byla aktualizována na verzi 10.1.0.
* Ctrl/Cmd + R se teď dají použít k aktualizaci aktuálně zaměřeného editoru. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Verze rozhraní API úložiště Azure Stack se změnila na 2017-04-17.
* Dialog spravovat přístup pro ADLS Gen2 teď zůstane maska synchronizovaná způsobem podobným ostatním nástrojům oprávnění POSIX. Uživatelské rozhraní vás také upozorní, pokud je provedena změna, která způsobí, že oprávnění uživatele nebo skupiny budou přesáhnout hranice masky. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Pro nahrávání AzCopy je teď povolený příznak pro výpočet a nastavení algoritmu hash MD5. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkce Preview

* Přihlášení toku kódu zařízení je teď dostupné ve verzi Preview. Pokud ho chcete povolit, přečtěte si "ve verzi Preview" → použít přihlášení ke toku kódu zařízení. Pro tuto funkci doporučujeme, aby všichni uživatelé, kteří mají potíže s prázdným systémem pro přihlášení, mohli tuto funkci vyzkoušet, protože se tak mohou považovat za spolehlivější způsob přihlašování.
* Průzkumník služby Storage integrovaná s AzCopy je aktuálně k dispozici ve verzi Preview. Pokud ho chcete povolit, přečtěte si "Preview" → "použití AzCopy pro lepší nahrávání a stahování objektů blob". Přenosy objektů BLOB dokončené s AzCopy by měly být rychlejší a více výkonné.

### <a name="fixes"></a>Opravy

* V dialogu zásady přístupu už nebude nastaveno datum vypršení platnosti u zásad přístupu k úložišti, které nemají vypršení platnosti. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* V dialogovém okně generovat SAS byly provedeny nějaké změny, aby se zajistilo, že uložené zásady přístupu se při generování SAS použijí správně. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Při pokusu o nahrání souboru zarovnanýho se systémem, který není 512 bajtů, do objektu blob stránky Průzkumník služby Storage nyní vystaví relevantnější chybu. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopírování kontejneru objektů blob, který využíval zobrazované jméno, by nedošlo k chybě. Nyní je použit skutečný název kontejneru objektů BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Pokus o provedení určitých akcí ve složce ADLS Gen2, která obsahovala znaky Unicode, by se nezdařil. Všechny akce by nyní měly fungovat. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Známé problémy

* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-170"></a>Verze 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Stáhnout Průzkumník služby Azure Storage 1.7.0
- [Průzkumník služby Azure Storage 1.7.0 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Průzkumník služby Azure Storage 1.7.0 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Průzkumník služby Azure Storage 1.7.0 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Nově můžete změnit vlastníka a vlastnící skupinu při správě přístupu pro ADLS Gen2 kontejner, soubor nebo složku.
* V systému Windows je teď aktualizace Průzkumník služby Storage v rámci produktu přírůstková instalace. To by mělo mít za následek rychlejší aktualizaci. Pokud dáváte přednost čisté instalaci, můžete si [instalační program](https://azure.microsoft.com/features/storage-explorer/) stáhnout sami a pak ručně nainstalovat. #1089

### <a name="preview-features"></a>Funkce Preview

* Přihlášení toku kódu zařízení je teď dostupné ve verzi Preview. Pokud ho chcete povolit, přečtěte si "ve verzi Preview" → použít přihlášení ke toku kódu zařízení. Pro tuto funkci doporučujeme, aby všichni uživatelé, kteří mají potíže s prázdným systémem pro přihlášení, mohli tuto funkci vyzkoušet, protože se tak mohou považovat za spolehlivější způsob přihlašování. #938
* Průzkumník služby Storage integrovaná s AzCopy je aktuálně k dispozici ve verzi Preview. Pokud ho chcete povolit, přečtěte si "Preview" → "použití AzCopy pro lepší nahrávání a stahování objektů blob". Přenosy objektů BLOB dokončené s AzCopy by měly být rychlejší a více výkonné.

### <a name="fixes"></a>Opravy

* Teď můžete zvolit typ objektu blob, který chcete nahrát, když je povolený AzCopy. #1111
* Pokud jste dříve povolili statické weby pro účet úložiště ADLS Gen2 a pak je připojili s názvem a klíčem, Průzkumník služby Storage by se nezjistilo, že byl povolen hierarchický obor názvů. To jsme opravili. #1081
* V editoru objektů BLOB se sestavuje řazení podle dnů uchovávání dat nebo je stav přerušeno. To jsme opravili. #1106
* Po 1.5.0 Průzkumník služby Storage už nečekat na dokončení kopií na straně serveru před tím, než se nahlásí po přejmenování nebo zkopírování & vložení. To jsme opravili. #976
* Při použití experimentální funkce AzCopy se příkaz zkopíroval po kliknutí na možnost zkopírovat příkaz do schránky, který se vždycky spustitelný sám na sebe. Všechny příkazy potřebné k ručnímu spuštění přenosu se teď zkopírují. #1079
* Dříve byly objekty blob ADLS Gen2 nedostupné, pokud jste za proxy. Důvodem byla chyba v nové síťové knihovně, kterou používá sada SDK pro úložiště. V 1.7.0 se provedl pokus o zmírnění tohoto problému, ale někteří lidé můžou dál sledovat problémy. Úplná Oprava bude vydána v budoucí aktualizaci. #1090
* V 1.7.0 teď dialog Uložit soubor správně pamatuje poslední umístění, do kterého jste soubor uložili. #16
* Na panelu Vlastnosti se jako typ účtu zobrazuje úroveň SKU účtu úložiště. To jsme opravili. #654
* V některých případech nebylo možné přerušit zapůjčení objektu blob, a to i v případě, že jste správně zadali název objektu BLOB. To jsme opravili. #1070

### <a name="known-issues"></a>Známé problémy

* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](./storage/common/storage-explorer-troubleshooting.md) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1.6.2 verze
1/9/2019

### <a name="hotfixes"></a>Opravy hotfix
* V 1.6.1 se entity přidané do ADLS Gen2 ACL podle identifikátorů ObjectId, které nebyly uživateli přidány jako skupiny. Nyní se jako skupiny přidají jenom skupiny a entity jako například podnikové aplikace andService objekty zabezpečení se přidají jako uživatelé. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Pokud účet úložiště ADLS Gen2 neobsahoval žádné kontejnery a byl připojen s názvem a klíčem, Průzkumník služby Storage by nerozpoznal, že byl účet úložiště ADLS Gen2. To jsme opravili. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* V 1.6.0 se konflikty při kopírování a vkládání nedotazují na řešení. Místo toho by mohlo dojít ke konfliktu kopírování. Teď budete požádáni o to, jak se má vyřešit. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Z důvodu omezení rozhraní API byla všechna ověření identifikátorů ObjectId v dialogovém okně Spravovat přístup zakázána. K ověření dojde nyní pouze u uživatelských UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně ADLS Gen2 spravovat přístup nelze změnit oprávnění pro skupinu. To jsme opravili. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidání podpory přetažení do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně Vlastnosti pro ADLS Gen2 soubory a složky někdy postrádá znak "/". To jsme opravili. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nepovede získat aktuální oprávnění pro ADLS Gen2 kontejner, soubor nebo složku, pak se tato chyba zobrazí v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH ve Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno připojit se teď zobrazí správně, pokud nejsou přihlášeni žádní uživatelé a nebyli připojeni žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládá vlastnosti pro objekty blob, které nejsou a soubory třídy HNS, do kódování hodnoty každé vlastnosti. Výsledkem je nepotřebné kódování hodnot, které obsahují pouze znaky ASCII. Hodnoty se teď zakódují jenom v případě, že obsahují znaky, které nejsou ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů BLOB bez rozhraní HNS by nebylo úspěšné, pokud se použilo SAS a SAS neměl oprávnění ke čtení. To jsme opravili. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. To jsme opravili. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by nedošlo k chybě při pokusu o stažení složky z ADLS Gen2 kontejneru objektů blob, pokud v názvu složky byly mezery. To jsme opravili. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl v 1.6.0 poškozen. Nyní je opraven. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
* Průzkumník služby Storage teď podporuje účty úložiště ADLS Gen2. Když Průzkumník služby Storage zjistí, že je pro účet úložiště povolený hierarchický obor názvů, zobrazí se vedle názvu svého účtu úložiště zpráva "(ADLS Gen2 Preview). Průzkumník služby Storage je schopný zjistit, jestli je v případě přihlášení povolený hierarchický obor názvů, nebo jestli jste k účtu úložiště připojili název a klíč. Pro ADLS Gen2 účty úložiště můžete použít Průzkumník služby Storage k těmto akcím:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností kontejneru a oprávnění (levá strana)
  * Zobrazení a navigace dat v kontejnerech
  * Vytvořit nové složky
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění k souborům a složkám (pravá strana).
    
    Jiné typické funkce objektů blob, například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je dostupná i v případě, že se přihlásíte. Navíc při práci v účtu úložiště ADLS Gen2 Průzkumník služby Storage použije AzCopy pro všechna nahrávání a stahování a pro všechny operace, pokud jsou k dispozici, použití přihlašovacích údajů pro název a klíč.
* Po poskytnutí silné zpětné vazby od uživatele se může přerušení zapůjčení znovu použít k přerušení zapůjčení více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Pokud přenášíte z ADLS Gen2 účtu úložiště, v případě, že některý ze souborů již existuje, AzCopy někdy dojde k chybě. Tato akce bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1.6.1 verze
12/18/2018

### <a name="hotfixes"></a>Opravy hotfix
* Z důvodu omezení rozhraní API byla všechna ověření identifikátorů ObjectId v dialogovém okně Spravovat přístup zakázána. K ověření dojde nyní pouze u uživatelských UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně ADLS Gen2 spravovat přístup nelze změnit oprávnění pro skupinu. To jsme opravili. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidání podpory přetažení do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně Vlastnosti pro ADLS Gen2 soubory a složky někdy postrádá znak "/". To jsme opravili. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nepovede získat aktuální oprávnění pro ADLS Gen2 kontejner, soubor nebo složku, pak se tato chyba zobrazí v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH ve Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno připojit se teď zobrazí správně, pokud nejsou přihlášeni žádní uživatelé a nebyli připojeni žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládá vlastnosti pro objekty blob, které nejsou a soubory třídy HNS, do kódování hodnoty každé vlastnosti. Výsledkem je nepotřebné kódování hodnot, které obsahují pouze znaky ASCII. Hodnoty se teď zakódují jenom v případě, že obsahují znaky, které nejsou ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů BLOB bez rozhraní HNS by nebylo úspěšné, pokud se použilo SAS a SAS neměl oprávnění ke čtení. To jsme opravili. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. To jsme opravili. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by nedošlo k chybě při pokusu o stažení složky z ADLS Gen2 kontejneru objektů blob, pokud v názvu složky byly mezery. To jsme opravili. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl v 1.6.0 poškozen. Nyní je opraven. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
* Průzkumník služby Storage teď podporuje účty úložiště ADLS Gen2. Když Průzkumník služby Storage zjistí, že je pro účet úložiště povolený hierarchický obor názvů, zobrazí se vedle názvu svého účtu úložiště zpráva "(ADLS Gen2 Preview). Průzkumník služby Storage je schopný zjistit, jestli je v případě přihlášení povolený hierarchický obor názvů, nebo jestli jste k účtu úložiště připojili název a klíč. Pro ADLS Gen2 účty úložiště můžete použít Průzkumník služby Storage k těmto akcím:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností kontejneru a oprávnění (levá strana)
  * Zobrazení a navigace dat v kontejnerech
  * Vytvořit nové složky
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění k souborům a složkám (pravá strana).
    
    Jiné typické funkce objektů blob, například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je dostupná i v případě, že se přihlásíte. Navíc při práci v účtu úložiště ADLS Gen2 Průzkumník služby Storage použije AzCopy pro všechna nahrávání a stahování a pro všechny operace, pokud jsou k dispozici, použití přihlašovacích údajů pro název a klíč.
* Po poskytnutí silné zpětné vazby od uživatele se může přerušení zapůjčení znovu použít k přerušení zapůjčení více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Pokud přenášíte z ADLS Gen2 účtu úložiště, v případě, že některý ze souborů již existuje, AzCopy někdy dojde k chybě. Tato akce bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1.6.0 verze
12/5/2018

### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
* Průzkumník služby Storage teď podporuje účty úložiště ADLS Gen2. Když Průzkumník služby Storage zjistí, že je pro účet úložiště povolený hierarchický obor názvů, zobrazí se vedle názvu svého účtu úložiště zpráva "(ADLS Gen2 Preview). Průzkumník služby Storage je schopný zjistit, jestli je v případě přihlášení povolený hierarchický obor názvů, nebo jestli jste k účtu úložiště připojili název a klíč. Pro ADLS Gen2 účty úložiště můžete použít Průzkumník služby Storage k těmto akcím:
  * Vytváření a odstraňování kontejnerů
  * Správa vlastností kontejneru a oprávnění (levá strana)
  * Zobrazení a navigace dat v kontejnerech
  * Vytvořit nové složky
  * Nahrávání, stahování, přejmenování a odstraňování souborů a složek
  * Správa vlastností a oprávnění k souborům a složkám (pravá strana).
    
    Jiné typické funkce objektů blob, například obnovitelné odstranění a snímky, nejsou aktuálně k dispozici. Správa oprávnění je dostupná i v případě, že se přihlásíte. Navíc při práci v účtu úložiště ADLS Gen2 Průzkumník služby Storage použije AzCopy pro všechna nahrávání a stahování a pro všechny operace, pokud jsou k dispozici, použití přihlašovacích údajů pro název a klíč.
* Po poskytnutí silné zpětné vazby od uživatele se může přerušení zapůjčení znovu použít k přerušení zapůjčení více objektů BLOB najednou.

### <a name="known-issues"></a>Známé problémy

* Pokud přenášíte z ADLS Gen2 účtu úložiště, v případě, že některý ze souborů již existuje, AzCopy někdy dojde k chybě. Tato akce bude opravena v nadcházející opravě hotfix.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>1.5.0 verze
10/29/2018

### <a name="new"></a>Nová

* Teď můžete pro nahrávání a stahování objektů BLOB používat [AzCopy v10 za účelem (Preview)](https://github.com/Azure/azure-storage-azcopy) . Pokud chcete tuto funkci povolit, přejděte do nabídky experimentální a pak klikněte na použít AzCopy pro zlepšení nahrávání a stahování objektů BLOB. Pokud je tato možnost povolená, AzCopy se použije v následujících scénářích:
   * Nahrajte složky a soubory do kontejnerů objektů blob, a to buď prostřednictvím panelu nástrojů, nebo přetažením.
   * Stahování složek a souborů, a to buď pomocí panelu nástrojů nebo místní nabídky.

* Kromě toho při použití AzCopy:
   * Můžete zkopírovat příkaz AzCopy, který se používá ke spuštění přenosu do schránky. Jednoduše klikněte na příkaz Kopírovat AzCopy do schránky v protokolu aktivit.
   * Po nahrání budete muset Editor objektů BLOB aktualizovat ručně.
   * Nahrávání souborů do připojených objektů BLOB se nepodporuje a soubory VHD se nahrají jako objekty blob stránky a všechny ostatní soubory se nahrají jako objekty blob bloku.
   * Chyby a konflikty, ke kterým dojde během nahrávání nebo stahování, se neprojeví až po dokončení nahrávání nebo stahování.

Nakonec bude podpora pro použití AzCopy se sdílenými složkami přizvána v budoucnosti.
* Průzkumník služby Storage teď používá elektronickou verzi 2.0.11.
* Přerušení zapůjčení se teď dají provádět jenom u jednoho objektu BLOB. Kromě toho musíte zadat název objektu blob, jehož zapůjčené adresy chcete rozdělit. Tato změna byla provedena, aby se snížila pravděpodobnost náhodného přerušení zapůjčení, zejména pro virtuální počítače. #394
* Pokud někdy dojde k problémům s přihlášením, můžete se teď pokusit o obnovení nastavení ověřování. Pokud chcete získat přístup k této funkci, přejděte do nabídky Help a klikněte na resetovat. #419

### <a name="fix"></a>Oprava

* Po poskytnutí silné zpětné vazby od uživatele byl výchozí uzel emulátoru znovu povolen. Další připojení emulátoru můžete přidat i přes dialogové okno připojit, ale pokud je váš emulátor nakonfigurovaný tak, aby používal výchozí porty, můžete použít taky uzel emulátor * výchozí porty v části místní & připojené nebo účty úložiště. #669
* Průzkumník služby Storage již nebude moci nastavit hodnoty metadat objektů blob, které mají počáteční nebo koncové mezery. #760
* Tlačítko pro přihlášení bylo na stejných stránkách dialogového okna připojit vždy povoleno. V případě potřeby je teď zakázaná. #761
* Rychlý přístup už v konzole negeneruje chybu, pokud se nepřidaly žádné položky rychlého přístupu.

### <a name="known-issues"></a>Známé problémy

* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stack prostředky může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1.4.4 verze
10/15/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět na odblokování uživatelů státní správy Azure USA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání číselníků teď používá animace CSS ke snížení množství GPU používaného Průzkumník služby Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externích prostředků, jako jsou například pro připojení a emulátory SAS, byly významně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který chcete připojit. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k několika místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky k rychlému přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Zásadu obnovitelného odstranění můžete nakonfigurovat kliknutím pravým tlačítkem myši na uzel kontejnery objektů BLOB pro váš účet úložiště.
   * V editoru objektů BLOB vyberte aktivní a odstraněné objekty BLOB a v rozevíracím seznamu vedle navigačního panelu zobrazte měkké odstraněné objekty blob.
   * Obnovit obnovitelné odstraněné objekty blob.

### <a name="fixes"></a>Opravy
* Akce konfigurace nastavení CORS už není dostupná u Premium Storage účtů, protože účty Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je teď vlastnost sdíleného přístupového podpisu. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce nastavit výchozí úroveň přístupu je teď dostupná pro účty úložiště BLOB a GPV2, které se připnuly k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech se Průzkumník služby Storage nepodařilo zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte emulátory, například Azure Storage emulátor nebo Azurite, budete je muset nechat naslouchat připojením na jejich výchozích portech. V opačném případě se Průzkumník služby Storage k nim nebude moci připojit.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>1.4.3 verze
10/11/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět na odblokování uživatelů státní správy Azure USA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání číselníků teď používá animace CSS ke snížení množství GPU používaného Průzkumník služby Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externích prostředků, jako jsou například pro připojení a emulátory SAS, byly významně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který chcete připojit. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k několika místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky k rychlému přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Zásadu obnovitelného odstranění můžete nakonfigurovat kliknutím pravým tlačítkem myši na uzel kontejnery objektů BLOB pro váš účet úložiště.
   * V editoru objektů BLOB vyberte aktivní a odstraněné objekty BLOB a v rozevíracím seznamu vedle navigačního panelu zobrazte měkké odstraněné objekty blob.
   * Obnovit obnovitelné odstraněné objekty blob.

### <a name="fixes"></a>Opravy
* Akce konfigurace nastavení CORS už není dostupná u Premium Storage účtů, protože účty Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je teď vlastnost sdíleného přístupového podpisu. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce nastavit výchozí úroveň přístupu je teď dostupná pro účty úložiště BLOB a GPV2, které se připnuly k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech se Průzkumník služby Storage nepodařilo zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte emulátory, například Azure Storage emulátor nebo Azurite, budete je muset nechat naslouchat připojením na jejich výchozích portech. V opačném případě se Průzkumník služby Storage k nim nebude moci připojit.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>1.4.2 verze
09/24/2018

### <a name="hotfixes"></a>Opravy hotfix
* Pokud chcete přidat podporu pro nové typy účtů Azure Storage, aktualizujte verzi rozhraní API pro správu prostředků Azure na 2018-07-01. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nová
* Přílohy externích prostředků, jako jsou například pro připojení a emulátory SAS, byly významně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který chcete připojit. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k několika místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky k rychlému přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Zásadu obnovitelného odstranění můžete nakonfigurovat kliknutím pravým tlačítkem myši na uzel kontejnery objektů BLOB pro váš účet úložiště.
   * V editoru objektů BLOB vyberte aktivní a odstraněné objekty BLOB a v rozevíracím seznamu vedle navigačního panelu zobrazte měkké odstraněné objekty blob.
   * Obnovit obnovitelné odstraněné objekty blob.

### <a name="fixes"></a>Opravy
* Akce konfigurace nastavení CORS už není dostupná u Premium Storage účtů, protože účty Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je teď vlastnost sdíleného přístupového podpisu. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce nastavit výchozí úroveň přístupu je teď dostupná pro účty úložiště BLOB a GPV2, které se připnuly k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech se Průzkumník služby Storage nepodařilo zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte emulátory, například Azure Storage emulátor nebo Azurite, budete je muset nechat naslouchat připojením na jejich výchozích portech. V opačném případě se Průzkumník služby Storage k nim nebude moci připojit.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Verze 1.4.1
08/28/2018

### <a name="hotfixes"></a>Opravy hotfix
* Při prvním spuštění Průzkumník služby Storage nedokázal vygenerovat klíč používaný k šifrování citlivých dat. To by způsobilo problémy při použití rychlého přístupu a připojení prostředků. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Pokud váš účet nevyžadoval MFA pro svého svého domovského tenanta, ale existoval pro některé další klienty, Průzkumník služby Storage by nedokázal seznamovat předplatná. Nyní po přihlášení pomocí účtu Průzkumník služby Storage vás vyzve k zadání přihlašovacích údajů a k provedení MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Průzkumník služby Storage se nepovedlo připojit prostředky z Azure Německo a vlády Azure USA. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Pokud jste se přihlásili ke dvěma účtům, které mají stejnou e-mailovou adresu, Průzkumník služby Storage někdy nedaří zobrazit vaše prostředky ve stromovém zobrazení. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na pomalejších počítačích s Windows by se mohla zobrazovat úvodní obrazovka, někdy to může trvat poměrně dlouho. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Dialog připojit se zobrazí i v případě, že byly připojeny účty nebo služby. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nová
* Přílohy externích prostředků, jako jsou například pro připojení a emulátory SAS, byly významně vylepšeny. Nyní můžete:
   * Přizpůsobte zobrazovaný název prostředku, který chcete připojit. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojte se k několika místním emulátorům pomocí různých portů. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidejte připojené prostředky k rychlému přístupu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Zásadu obnovitelného odstranění můžete nakonfigurovat kliknutím pravým tlačítkem myši na uzel kontejnery objektů BLOB pro váš účet úložiště.
   * V editoru objektů BLOB vyberte aktivní a odstraněné objekty BLOB a v rozevíracím seznamu vedle navigačního panelu zobrazte měkké odstraněné objekty blob.
   * Obnovit obnovitelné odstraněné objekty blob.

### <a name="fixes"></a>Opravy
* Akce konfigurace nastavení CORS už není dostupná u Premium Storage účtů, protože účty Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Pro připojené služby SAS je teď vlastnost sdíleného přístupového podpisu. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce nastavit výchozí úroveň přístupu je teď dostupná pro účty úložiště BLOB a GPV2, které se připnuly k rychlému přístupu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech se Průzkumník služby Storage nepodařilo zobrazit účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte emulátory, například Azure Storage emulátor nebo Azurite, budete je muset nechat naslouchat připojením na jejich výchozích portech. V opačném případě se Průzkumník služby Storage k nim nebude moci připojit.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>1.3.0 verze
07/09/2018

### <a name="new"></a>Nová
* Přístup k kontejnerům $web používaných statickými weby se teď podporuje. Díky tomu můžete snadno nahrávat a spravovat soubory a složky používané vaším webem. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Změnila se uspořádání panelu aplikace v macOS. Změny zahrnují nabídku soubor, několik klávesových zkratek a několik nových příkazů v nabídce aplikace. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Koncový bod autority pro přihlášení ke službě Azure US státní správu byl změněn na https://login.microsoftonline.us/
* Usnadnění: když je čtečka obrazovky aktivní, navigace pomocí klávesnice teď funguje s tabulkami použitými pro zobrazení položek na pravé straně. Pomocí kláves se šipkami můžete procházet řádky a sloupce, zadat pro vyvolání výchozích akcí, klávesu kontextové nabídky pro otevření kontextové nabídky pro položku a přesunutím nebo ovládáním pro výběr. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Opravy
*  Na některých počítačích bylo spuštění podřízených procesů trvat dlouhou dobu. V takovém případě se zobrazí chyba "podřízený proces se nepovedlo včas spustit". Čas přidělený pro spuštění podřízeného procesu se nyní zvýšil z 20 na 90 sekund. Pokud tento problém pořád ovlivníte, uveďte komentář k propojenému problému GitHubu. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Při použití SAS, který nemá oprávnění ke čtení, nebylo možné nahrát velký objekt BLOB. Logika pro nahrání se v tomto scénáři změnila tak, aby fungovala. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Nastavením úrovně veřejného přístupu pro kontejner by se odebraly všechny zásady přístupu a naopak. Při nastavování obou z nich jsou teď zachované zásady přístupu a úrovně veřejného přístupu. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" bylo zkráceno v dialogovém okně Vlastnosti. To jsme opravili. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* V dialogovém okně vytvořit nový adresář chyběla předpona "Průzkumník služby Microsoft Azure Storage-". To jsme opravili. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Usnadnění: Dialog Přidat entitě se při použití VoiceOver obtížně navigoval. Vylepšení byla provedena. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Usnadnění: barva pozadí tlačítka pro sbalení a rozbalení pro podokno akce a vlastnosti byla nekonzistentní s podobnými ovládacími prvky uživatelského rozhraní v Vysoký kontrast černém motivu. Barva byla změněna. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Usnadnění: v Vysoký kontrast černý motiv se styl fokusu pro tlačítko X v dialogu Vlastnosti nezobrazuje. To jsme opravili. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Přístupnost: na kartách akce a vlastnosti chybí několik hodnot ARIA, které vedly k subparmu čtečce obrazovky. V tuto chvíli se přidaly chybějící hodnoty Aria. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Usnadnění: sbaleným uzlům stromu na levé straně nebyla udělena hodnota NEPRAVDA, která byla rozšířena na standard ARIA. To jsme opravili. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Známé problémy
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Pokud chcete tento problém obejít, stačí aktualizovat uzel skupiny. Další informace najdete v [tomto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* Azure Stack nepodporuje následující funkce a pokus o jejich použití při práci s Azure Stack může vést k neočekávaným chybám:
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1.2.0 verze
06/12/2018

### <a name="new"></a>Nová
* Pokud se Průzkumník služby Storage nepovede načíst odběry jenom z podmnožiny klientů, pak se zobrazí všechny úspěšně načtené odběry spolu s chybovou zprávou specifickou pro klienty, u kterých došlo k chybě. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Pokud je v systému Windows k dispozici aktualizace, můžete se teď rozhodnout pro možnost aktualizovat při zavření. Pokud je tato možnost vybrána, instalační program pro aktualizaci bude spuštěn po zavření Průzkumník služby Storage. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Při prohlížení snímku sdílené složky byl do kontextové nabídky editoru sdílené složky přidán snímek pro obnovení. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Tlačítko Vymazat frontu je teď vždycky zapnuté. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Podpora pro přihlašování ke službě ADFS Azure Stack byla znovu povolena. Vyžaduje se Azure Stack verze 1804 nebo vyšší. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Opravy
* Pokud jste si prohlédli snímky pro sdílenou složku, jejíž název byl prefixem jiné sdílené složky ve stejném účtu úložiště, budou také uvedeny snímky pro ostatní sdílené složky. Tento problém byl opraven. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Při obnovení souboru ze snímku sdílené složky při jeho připojení prostřednictvím SAS dojde k chybě. Tento problém byl opraven. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Při prohlížení snímků pro objekt BLOB byla povolena akce povýšení snímku při výběru základního objektu BLOB a jednoho snímku. Akce je nyní povolena pouze v případě, že je vybrán jeden snímek. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Pokud byla spuštěna jedna úloha (například stažení objektu BLOB) a později se nezdařila, nebude automaticky opakována, dokud nespustíte jinou úlohu stejného typu. Všechny úlohy by nyní měly automaticky opakovat bez ohledu na to, kolik úloh jste zařadili do fronty.
* Editory otevřené pro nově vytvořené kontejnery objektů BLOB v účtech GPV2 a Blob Storage neobsahují sloupec úrovně přístupu. Tento problém byl opraven. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Pokud byl k účtu úložiště nebo kontejneru objektů BLOB připojen pomocí SAS, někdy se sloupec úrovně přístupu nemusí zobrazovat. Sloupec se teď bude zobrazovat vždycky, ale s prázdnou hodnotou, pokud není nastavená žádná úroveň přístupu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Nastavení úrovně přístupu nově nahraného objektu blob bloku bylo zakázáno. Tento problém byl opraven. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Pokud se tlačítko "ponechat otevřené karty" vyvolalo pomocí klávesnice, pak se fokus klávesnice ztratí. Nyní se fokus přesune na kartu, která byla otevřená. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Pro dotaz v Tvůrce dotazů neposkytl VoiceOver použitelný Popis aktuálního operátoru. Je teď výstižnější. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Odkazy na stránkování různých editorů nebyly popisné. Změnily se tak, aby byly výstižnější. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* V dialogovém okně Přidat entitu VoiceOver neoznamuje, ke kterému sloupci byl vstupní element součástí. Název aktuálního sloupce je nyní zahrnut do popisu elementu. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Přepínače a zaškrtávací políčka při zaměření neobsahovaly viditelné ohraničení. Tento problém byl opraven. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte emulátory, například Azure Storage emulátor nebo Azurite, budete je muset nechat naslouchat připojením na jejich výchozích portech. V opačném případě se Průzkumník služby Storage k nim nebude moci připojit.
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1.1.0 verze
05/09/2018

### <a name="new"></a>Nová
* Průzkumník služby Storage teď podporuje použití Azurite. Poznámka: připojení k Azurite je pevně zakódované do výchozích koncových bodů vývoje.
* Průzkumník služby Storage teď podporuje úrovně přístupu jenom pro objekty BLOB a účty úložiště GPV2. Další informace o úrovních přístupu najdete [tady](./storage/blobs/storage-blob-storage-tiers.md).
* Čas zahájení již není při generování SAS vyžadován.

### <a name="fixes"></a>Opravy
* Načítání předplatných pro státní správu USA bylo přerušeno. Tento problém byl opraven. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Čas vypršení platnosti zásad přístupu nebyl správně uložen. Tento problém byl opraven. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Při generování adresy URL SAS pro položku v kontejneru se název položky nepřipojí k adrese URL. Tento problém byl opraven. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Při vytváření SAS vypršela doba vypršení platnosti v minulosti výchozí hodnota. To bylo způsobeno tím, že Průzkumník služby Storage jako výchozí hodnoty používali čas posledního použití času zahájení a vypršení platnosti. Nyní při každém otevření dialogového okna SAS se vygeneruje nová sada výchozích hodnot. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Při kopírování mezi účty úložiště se vygeneruje 24 hodin SAS. Pokud byla kopie naposledy delší než 24 hodin, kopie by se nezdařila. Zvýšili jsme si přidružení zabezpečení na poslední 1 týden, aby se snížila pravděpodobnost neúspěšného kopírování z důvodu vypršení platnosti SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* U některých aktivit klikněte na zrušit, pokud nebudete vždycky fungovat. Tento problém byl opraven. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* U některých aktivit došlo k chybě přenosové rychlosti. Tento problém byl opraven. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Pravopis "předchozí" v nabídce zobrazení byl nesprávný. Je teď správně napsaný. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Poslední stránka instalační služby systému Windows obsahovala tlačítko Další. Byl změněn na tlačítko Dokončit. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Pro tlačítka v dialogových oknech nebyl při použití černého motivu HC viditelný fokus karty. Teď je zobrazený. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Velká a malá písmena "automatické rozpoznávání" pro akce v protokolu aktivit byly nesprávné. Je teď správná. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Při odstraňování entity z tabulky se zobrazí dialogové okno s výzvou k potvrzení, že se zobrazila ikona chyby. Dialog teď používá ikonu upozornění. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte VS pro Mac a někdy jste vytvořili vlastní konfiguraci AAD, možná se nebudete moct přihlásit. Pokud chcete tento problém obejít, odstraňte obsah ~/. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ještě neplně implementovala všechna rozhraní API úložiště. Z tohoto důvodu může při použití Azurite pro vývojové úložiště dojít k neočekávaným chybám nebo chování.
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Nahrávání ze složky OneDrivu nefunguje kvůli chybě v NodeJS. Chyba byla opravena, ale ještě není integrovaná do elektronu.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>1.0.0 verze
04/16/2018

### <a name="new"></a>Nová
* Rozšířené ověřování, které umožňuje Průzkumník služby Storage použít stejné úložiště účtů jako Visual Studio 2017. Pokud chcete používat tuto funkci, budete se muset znovu přihlásit k vašim účtům a znovu nastavit filtrované odběry.
* U účtů Azure Stack zálohovaných službou AAD Průzkumník služby Storage nyní získá Azure Stack předplatná, pokud je povolená možnost cílový Azure Stack. Už nemusíte vytvářet vlastní prostředí pro přihlášení.
* Bylo přidáno několik klávesových zkratek umožňujících rychlejší navigaci. Patří mezi ně přepínání různých panelů a přesun mezi editory. Další podrobnosti najdete v nabídce zobrazení.
* Průzkumník služby Storage zpětná vazba nyní bydlí na GitHubu. Na stránku problémů se dostanete tak, že kliknete na tlačítko zpětná vazba v dolní části doleva nebo přejdete na [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Nebojte se vytvářet návrhy, nahlásit problémy, klást dotazy nebo opustit jakoukoli jinou formu zpětné vazby.
* Pokud používáte k problémům s certifikátem TLS/SSL a nemůžete najít problematický certifikát, můžete teď Průzkumník služby Storage spustit z příkazového řádku s `--ignore-certificate-errors` příznakem. Při spuštění s tímto příznakem Průzkumník služby Storage bude ignorovat chyby certifikátu TLS/SSL.
* V kontextové nabídce pro položky BLOB a soubor je teď možnost stáhnout.
* Vylepšená podpora pro usnadnění přístupu a čtečky obrazovky. Pokud se spoléháte na funkce přístupnosti, najdete další informace v [dokumentaci k usnadnění](./vs-azure-tools-storage-explorer-accessibility.md) .
* Průzkumník služby Storage nyní používá elektronickou 1.8.3

### <a name="breaking-changes"></a>Zásadní změny
* Průzkumník služby Storage se přepnula na novou knihovnu ověřování. Jako součást přepínače do knihovny se budete muset znovu přihlásit k vašim účtům a znovu nastavit filtrované odběry.
* Metoda použitá k šifrování citlivých dat se změnila. To může vést k tomu, že některé položky rychlého přístupu je potřeba znovu přidat nebo některé z vašich připojených prostředků, které potřebujete znovu připojit.

### <a name="fixes"></a>Opravy
* Někteří uživatelé na proxy serverech by nastavili odesílání objektů BLOB skupin nebo se jejich stahování přerušilo chybovou zprávou "nelze přeložit". Tento problém byl opraven.
* Pokud bylo při použití přímého odkazu vyžadováno přihlášení, kliknutím na výzvu k přihlášení se zobrazí prázdné dialogové okno. Tento problém byl opraven.
* Pokud se v systému Linux nedaří spustit Průzkumník služby Storage kvůli chybě procesu GPU, budete teď informováni o této chybě, dozvíte se, že byste měli použít přepínač--Disable-GPU a Průzkumník služby Storage se pak automaticky restartuje s povoleným přepínačem.
* V dialogovém okně zásady přístupu bylo obtížné získat neplatnou identitu zásad přístupu. Neplatná ID zásad přístupu jsou teď pro lepší viditelnost podrobnější.
* Protokol aktivit někdy obsahuje velké oblasti prázdných znaků mezi různými částmi aktivity. Tento problém byl opraven.
* Pokud jste v editoru dotazů na tabulku opustili klauzuli časového razítka v neplatném stavu a následně jste se pokusili o úpravu jiné klauzule, Editor se zablokuje. Editor nyní obnoví klauzuli timestamp do posledního platného stavu při zjištění změny v jiné klauzuli.
* Pokud jste pozastavili zápis do vyhledávacího dotazu ve stromovém zobrazení, hledání by vedlo k zahájení a fokus by byl odcizen z textového pole. Nyní musíte explicitně spustit vyhledávání stisknutím klávesy ENTER nebo kliknutím na tlačítko Spustit hledání.
* Příkaz získání sdíleného přístupového podpisu se někdy zakáže, když kliknete pravým tlačítkem na soubor ve sdílené složce. Tento problém byl opraven.
* Pokud byl uzel stromu prostředků s fokusem filtrován při hledání, nemůžete se do stromu zdrojů vztahovat karta a pomocí kláves se šipkami můžete procházet strom prostředků. Pokud je teď uzel prioritní strom prostředků skrytý, bude se automaticky zaměřit na první uzel ve stromové struktuře prostředků.
* Oddělovač navíc by byl někdy viditelný na panelu nástrojů editoru. Tento problém byl opraven.
* Textové pole s popisem cesty bude někdy přetečení. Tento problém byl opraven.
* Editory objektů BLOB a sdílených složek se někdy průběžně aktualizují při nahrávání mnoha souborů najednou. Tento problém byl opraven.
* Funkce statistiky složky neměla žádný účel v zobrazení správy snímků sdílené složky. Je teď zakázaný.
* V systému Linux se nabídka soubor nezobrazila. Tento problém byl opraven.
* Když nahráváte složku do sdílené složky, ve výchozím nastavení se nahrál jenom obsah složky. Nyní je výchozím chováním nahrání obsahu složky do složky ve sdílené složce.
* Řazení tlačítek v několika dialogových oknech bylo obráceno. Tento problém byl opraven.
* Různé opravy související se zabezpečením.

### <a name="known-issues"></a>Známé problémy
* Ve výjimečných případech se fokus stromu může zablokovat na rychlý přístup. Pokud chcete fokus odlepit, můžete aktualizovat vše.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0.9.6 verze
02/28/2018

### <a name="fixes"></a>Opravy
* Problém zabránil v uvedení očekávaných objektů BLOB nebo souborů v editoru. Tento problém byl opraven.
* Problém způsobil přepínání mezi zobrazeními snímků a nesprávným zobrazením položek. Tento problém byl opraven.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty ADFS.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0.9.5 verze
02/06/2018

### <a name="new"></a>Nová

* Podpora snímků sdílených složek:
    * Vytvářejte a spravujte snímky pro sdílené složky.
    * Snadné přepínání zobrazení mezi snímky sdílených složek při prozkoumávání souborů.
    * Obnovte předchozí verze vašich souborů.
* Podpora verze Preview pro Azure Data Lake Store:
    * Připojte se k prostředkům ADLS napříč několika účty.
    * Připojte se k prostředkům ADLS a sdílejte je pomocí identifikátorů URI ADL.
    * Provede rekurzivní operace se základními soubory a složkami.
    * Připněte jednotlivé složky k rychlému přístupu.
    * Zobrazit statistiku složky.

### <a name="fixes"></a>Opravy
* Vylepšení výkonu při spuštění.
* Různé opravy chyb.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty ADFS.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Verze 0.9.4 a 0.9.3
01/21/2018

### <a name="new"></a>Nová
* Existující Průzkumník služby Storage okno se znovu použije, když:
    * Otevírání přímých odkazů generovaných v Průzkumník služby Storage.
    * Otevírá se Průzkumník služby Storage z portálu.
    * Otevírání Průzkumník služby Storage z rozšíření Azure Storage VS Code (už brzy)
* Přidání možnosti otevření nového okna Průzkumník služby Storage v rámci Průzkumník služby Storage.
    * V případě systému Windows je v nabídce soubor a v místní nabídce na hlavním panelu k dispozici možnost nové okno.
    * Pro Mac je v nabídce aplikace dostupná možnost nové okno.

### <a name="fixes"></a>Opravy
* Opravili jsme problém se zabezpečením. Upgradujte prosím na 0.9.4, který je nejdřívějším pohodlím.
* Staré aktivity nebyly vhodně vyčištěny. To ovlivnilo výkon dlouhodobě spuštěných úloh. Nyní se vyčistí správně.
* Akce zahrnující velký počet souborů a adresářů občas způsobí, že Průzkumník služby Storage zablokovat. Požadavky na Azure pro sdílené složky jsou teď omezené na omezení používání systémových prostředků.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty ADFS.
* Klávesové zkratky pro "zobrazení Průzkumníka" a "Zobrazit správu účtu" by měly být Ctrl/Cmd + Shift + E a Ctrl/Cmd + Shift + A v uvedeném pořadí.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0.9.2 verze
11/01/2017

### <a name="hotfixes"></a>Opravy hotfix
* Při úpravách hodnot EDM. DateTime pro entity tabulky v závislosti na místním časovém pásmu byly pravděpodobně neočekávané změny dat. Editor nyní používá jednoduché textové pole, které poskytuje přesné a konzistentní řízení nad hodnotami EDM. DateTime.
* Nahrání/stažení skupiny objektů blob, pokud se připojí s názvem a klíčem, se nespustí. Tento problém byl opraven.
* Dřív Průzkumník služby Storage vás při výběru jednoho nebo více předplatných účtů zobrazila výzva k opětovnému ověření zastaralého účtu. Nyní Průzkumník služby Storage vás vyzve i v případě, že je účet plně filtrován.
* Doména koncových bodů pro vládu Azure US byla nesprávná. Byla opravena.
* Na panelu Správa účtů bylo někdy těžké kliknout na tlačítko použít. Tato situace by již neměla nastat.

### <a name="new"></a>Nová
* Podpora verze Preview pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytváření databází a kolekcí
    * Manipulace s daty
    * Dotazování, vytváření a odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelsky definovaných funkcí nebo triggerů
    * Použití připojovacích řetězců pro připojení a správu databází
* Vylepšili jsme výkon nahrávání nebo stahování mnoha malých objektů BLOB.
* Pokud dojde k chybám ve skupině pro nahrání objektů BLOB nebo ve skupině pro stahování objektů blob, přidejte akci opakovat vše.
* Průzkumník služby Storage teď bude pozastavit iteraci během nahrávání nebo stahování objektu blob, pokud zjistíte, že se ztratilo síťové připojení. Po opětovném navázání připojení k síti pak můžete pokračovat v iteraci.
* Přidání možnosti "Zavřít vše", "Zavřít ostatní" a "Zavřít" karty prostřednictvím kontextové nabídky.
* Průzkumník služby Storage nyní používá nativní dialogová okna a nativní místní nabídky.
* Průzkumník služby Storage je nyní k dispozici. Mezi vylepšení patří:
    * Vylepšená podpora čtečky obrazovky, pro NVDA ve Windows a pro VoiceOver v Mac
    * Vylepšení vysokého kontrastu
    * Opravy klávesových zkratek a fokusu klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt BLOB s neplatným názvem souboru systému Windows, operace by se nezdařila. Průzkumník služby Storage teď zjistí, jestli je název objektu BLOB neplatný, a zeptejte se, jestli se má objekt BLOB zakódovat nebo přeskočit. Průzkumník služby Storage také zjistí, zda se zdá, že název souboru má být kódovaný a zda jej chcete před odesláním dekódovat.
* Během nahrávání objektu BLOB by se někdy nepovedlo správně aktualizovat editor cílového kontejneru objektů BLOB. Tento problém byl opraven.
* Podpora několika forem připojovacích řetězců a identifikátorů URI SAS je převedená. Vyřešili jsme všechny známé problémy, ale pošlete nám prosím svůj názor, pokud se setkáte s dalšími problémy.
* Oznámení o aktualizaci bylo pro některé uživatele v 0.9.0 poškozeno. Tento problém byl vyřešen a pro ty, kterých se tato chyba týká, můžete ručně stáhnout nejnovější [verzi Průzkumník služby Storage.](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty ADFS.
* Klávesové zkratky pro "zobrazení Průzkumníka" a "Zobrazit správu účtu" by měly být Ctrl/Cmd + Shift + E a Ctrl/Cmd + Shift + A v uvedeném pořadí.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Verze 0.9.1 a 0.9.0
10/20/2017
### <a name="new"></a>Nová
* Podpora verze Preview pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytváření databází a kolekcí
    * Manipulace s daty
    * Dotazování, vytváření a odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelsky definovaných funkcí nebo triggerů
    * Použití připojovacích řetězců pro připojení a správu databází
* Vylepšili jsme výkon nahrávání nebo stahování mnoha malých objektů BLOB.
* Pokud dojde k chybám ve skupině pro nahrání objektů BLOB nebo ve skupině pro stahování objektů blob, přidejte akci opakovat vše.
* Průzkumník služby Storage teď bude pozastavit iteraci během nahrávání nebo stahování objektu blob, pokud zjistíte, že se ztratilo síťové připojení. Po opětovném navázání připojení k síti pak můžete pokračovat v iteraci.
* Přidání možnosti "Zavřít vše", "Zavřít ostatní" a "Zavřít" karty prostřednictvím kontextové nabídky.
* Průzkumník služby Storage nyní používá nativní dialogová okna a nativní místní nabídky.
* Průzkumník služby Storage je nyní k dispozici. Mezi vylepšení patří:
    * Vylepšená podpora čtečky obrazovky, pro NVDA ve Windows a pro VoiceOver v Mac
    * Vylepšení vysokého kontrastu
    * Opravy klávesových zkratek a fokusu klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt BLOB s neplatným názvem souboru systému Windows, operace by se nezdařila. Průzkumník služby Storage teď zjistí, jestli je název objektu BLOB neplatný, a zeptejte se, jestli se má objekt BLOB zakódovat nebo přeskočit. Průzkumník služby Storage také zjistí, zda se zdá, že název souboru má být kódovaný a zda jej chcete před odesláním dekódovat.
* Během nahrávání objektu BLOB by se někdy nepovedlo správně aktualizovat editor cílového kontejneru objektů BLOB. Tento problém byl opraven.
* Podpora několika forem připojovacích řetězců a identifikátorů URI SAS je převedená. Vyřešili jsme všechny známé problémy, ale pošlete nám prosím svůj názor, pokud se setkáte s dalšími problémy.
* Oznámení o aktualizaci bylo pro některé uživatele v 0.9.0 poškozeno. Tento problém byl vyřešen a pro ty, kterých se tato chyba týká, můžete ručně stáhnout nejnovější verzi Průzkumník služby Storage [sem](https://azure.microsoft.com/features/storage-explorer/) .

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty ADFS.
* Klávesové zkratky pro "zobrazení Průzkumníka" a "Zobrazit správu účtu" by měly být Ctrl/Cmd + Shift + E a Ctrl/Cmd + Shift + A v uvedeném pořadí.
* Při cílení na Azure Stack se může stát, že se podaří odeslat určité soubory jako doplňovací objekty blob.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pracovní prostředí, které používá Průzkumník služby Storage, má potíže s některými hardwarovými akceleraci GPU (Graphics Processing Unit). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním `--disable-gpu` přepínače:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0.8.16 verze
8/21/2017

### <a name="new"></a>Nová
* Když otevřete objekt blob, Průzkumník služby Storage vás vyzve k nahrání staženého souboru, pokud je zjištěna změna.
* Vylepšené prostředí pro přihlašování Azure Stack
* Zvýšil se výkon při nahrávání nebo stahování několika malých souborů současně.


### <a name="fixes"></a>Opravy
* U některých typů objektů BLOB se v případě konfliktu nahrávání v některých případech v případě konfliktu nahrávání někdy vytvoří restartování nahrávání.
* Ve verzi 0.8.15 by nahrání bylo někdy v 99%.
* Pokud se při nahrávání souborů do sdílené složky rozhodnete odeslat do adresáře, který ještě neexistoval, vaše nahrávání selže.
* Průzkumník služby Storagea nesprávně vygenerovala časová razítka pro signatury sdíleného přístupu a dotazy na tabulky.


### <a name="known-issues"></a>Známé problémy
* Použití názvu a připojovacího řetězce klíče aktuálně nefunguje. Bude opravena v příští verzi. Až potom můžete použít možnost připojit s názvem a klíčem.
* Pokud se pokusíte otevřít soubor s neplatným názvem souboru systému Windows, bude se při stahování vycházet chyba souboru, který nebyl nalezen.
* Po kliknutí na tlačítko Storno u úkolu může trvat nějakou dobu, než se tato úloha zruší. Toto je omezení knihovny uzlů Azure Storage.
* Po dokončení nahrávání objektu BLOB se aktualizuje karta, která iniciovala nahrávání. Jedná se o změnu z předchozího chování a také způsobí, že se vrátíte zpět do kořenového adresáře kontejneru, ve kterém se nacházíte.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby se toto rozhodnutí Průzkumník služby Storage zapomenout.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje pro filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Pro uživatele, kteří používají Ubuntu 14,04, je potřeba zajistit, aby byla služba RSZ aktuální. můžete to udělat spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele na Ubuntu 17,04 bude nutné nainstalovat GConf – to lze provést spuštěním následujících příkazů a restartováním počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0.8.14 verze
06/22/2017

### <a name="new"></a>Nová

* Aktualizovaná elektronická verze na 1.7.2, aby bylo možné využívat několik důležitých aktualizací zabezpečení
* Teď můžete rychle přejít do Průvodce odstraňováním potíží online v nabídce Nápověda.
* [Průvodce][2] odstraňováním potíží s Průzkumník služby Storage
* [Pokyny][3] pro připojení k předplatnému Azure Stack

### <a name="known-issues"></a>Známé problémy

* Tlačítka v potvrzovacím dialogovém okně Odstranit složku se neregistrují kliknutím myši na Linux. Chcete-li použít klávesu ENTER, použijte alternativní postup.
* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby bylo rozhodnutí Průzkumník služby Storage zapomenout.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se nahrávají najednou, může dojít k chybám.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílení souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Instalace Ubuntu 14,04 vyžaduje aktualizaci nebo upgrade verze RSZ – níže je postup upgradu:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13 verze
05/12/2017

#### <a name="new"></a>Nová

* [Průvodce][2] odstraňováním potíží s Průzkumník služby Storage
* [Pokyny][3] pro připojení k předplatnému Azure Stack

#### <a name="fixes"></a>Opravy

* Opraveno: nahrání souboru má vysokou pravděpodobnost způsobilo nedostatek paměti.
* Opraveno: teď se můžete přihlásit pomocí kódu PIN nebo čipové karty.
* Opraveno: otevřít na portálu teď funguje s Azure Čína 21Vianet, Azure Německo, Azure US vládou a Azure Stack
* Opraveno: při nahrávání složky do kontejneru objektů BLOB by v některých případech došlo k chybě "Neplatná operace"
* Opraveno: při správě snímků byl zakázán výběr vše.
* Opraveno: metadata základního objektu BLOB se můžou po zobrazení vlastností jeho snímků přepsat.

#### <a name="known-issues"></a>Známé problémy

* Pokud zvolíte špatný certifikát PIN nebo SmartCard, budete se muset restartovat, aby bylo rozhodnutí Průzkumník služby Storage zapomenout.
* Při přiblížení nebo oddálení může úroveň přiblížení po obnovení na výchozí úroveň.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se nahrávají najednou, může dojít k chybám.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Instalace Ubuntu 14,04 vyžaduje aktualizaci nebo upgrade verze RSZ – níže je postup upgradu:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Verze 0.8.12 a 0.8.11 a 0.8.10
04/07/2017

#### <a name="new"></a>Nová

* Při instalaci aktualizace z oznámení aktualizace se teď Průzkumník služby Storage automaticky zavřou.
* Místní přístup poskytuje vylepšené prostředí pro práci s často používanými prostředky.
* V editoru kontejnerů objektů BLOB teď můžete vidět, do kterého virtuálního počítače patří pronajatý objekt BLOB.
* V tuto chvíli můžete sbalit panel na levé straně.
* Zjišťování se teď spouští ve stejnou dobu jako stahování.
* Pro zobrazení velikosti prostředku nebo výběru použijte statistiku v kontejneru objektů blob, sdílení souborů a editorech tabulek.
* Nyní se můžete přihlásit k účtům Azure Stack na základě Azure Active Directory (AAD).
* Soubory archivu teď můžete nahrávat přes 32MB do účtů Premium Storage.
* Vylepšená podpora usnadnění přístupu
* Přidáním důvěryhodných certifikátů Standard-64 s kódováním TLS/SSL teď můžete přidat do nabídky upravit – &gt; certifikáty SSL – &gt; importovat certifikáty.

#### <a name="fixes"></a>Opravy

* Opraveno: po aktualizaci přihlašovacích údajů účtu se stromové zobrazení někdy automaticky neaktualizuje.
* Opraveno: vygenerování SAS pro fronty a tabulky emulátoru by způsobilo neplatnou adresu URL.
* Opraveno: účty úložiště úrovně Premium se teď dají rozšířit, když je povolený proxy server.
* Opraveno: tlačítko použít na stránce Správa účtů nebude fungovat, pokud jste vybrali 1 nebo 0 účtů.
* Opraveno: nahrávání objektů blob, které vyžadují řešení konfliktů, může v 0.8.11 selhat.
* Opraveno: odeslání zpětné vazby bylo v 0.8.11 přerušeno – opraveno v 0.8.12

#### <a name="known-issues"></a>Známé problémy

* Po upgradu na 0.8.10 budete muset aktualizovat všechny přihlašovací údaje.
* Při přiblížení nebo oddálení může úroveň přiblížení chvíli obnovit na výchozí úroveň.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se dají nahrát současně, může dojít k chybám.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity jsou během přejmenování zachované.
* I když Azure Stack v současné době nepodporuje sdílené složky souborů, uzel sdílené složky se pořád zobrazuje pod připojeným účtem úložiště Azure Stack.
* Instalace Ubuntu 14,04 vyžaduje aktualizaci nebo upgrade verze RSZ – níže je postup upgradu:

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

* Průzkumník služby Storage 0.8.9 bude automaticky stahovat nejnovější verzi aktualizací.
* Oprava hotfix: použití identifikátoru URI SAS generovaného portálem pro připojení účtu úložiště by způsobilo chybu.
* Teď můžete vytvářet, spravovat a propagovat snímky objektů BLOB.
* Nyní se můžete přihlásit ke službě Azure Čína 21Vianet, Azure Německo a Azure pro státní správu USA.
* Nyní můžete změnit úroveň přiblížení. Pomocí možností v nabídce zobrazení můžete přiblížit, oddálit a obnovovat přiblížení.
* V metadatech uživatelů pro objekty BLOB a soubory se teď podporují znaky Unicode.
* Vylepšení usnadnění.
* Poznámky k verzi další verze si můžete prohlédnout v oznámení o aktualizacích. Aktuální poznámky k verzi si můžete zobrazit také v nabídce Help.

#### <a name="fixes"></a>Opravy

* Opraveno: číslo verze je teď správně zobrazené v Ovládacích panelech Windows.
* Opraveno: hledání již není omezeno na 50 000 uzlů.
* Opraveno: nahrání do sdílené složky se trvale provedlo, pokud cílový adresář ještě neexistuje.
* Opraveno: Vylepšená stabilita pro dlouhodobá nahrávání a stahování

#### <a name="known-issues"></a>Známé problémy

* Při přiblížení nebo oddálení může úroveň přiblížení chvíli obnovit na výchozí úroveň.
* Rychlý přístup funguje jenom s položkami na základě předplatného. Místní prostředky nebo prostředky připojené pomocí klíče nebo tokenu SAS nejsou v této verzi podporované.
* V závislosti na tom, kolik prostředků máte, může rychlý přístup k cílovému prostředku přejít na několik sekund.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se dají nahrát současně, může dojít k chybám.

12/16/2016
### <a name="version-087"></a>0.8.7 verze

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nová

* Můžete zvolit způsob řešení konfliktů na začátku aktualizace, stažení nebo kopírování relace v okně aktivity.
* Pokud chcete zobrazit úplnou cestu k prostředku úložiště, najeďte myší na kartu.
* Když kliknete na kartu, synchronizuje se s jejím umístěním v navigačním podokně na levé straně.

#### <a name="fixes"></a>Opravy

* Opraveno: Průzkumník služby Storage je teď důvěryhodná aplikace na Macu.
* Opraveno: Ubuntu 14,04 se znovu podporuje.
* Opraveno: někdy při načítání předplatných bliká uživatelské rozhraní pro přidání účtu.
* Opraveno: někdy ne všechny prostředky úložiště byly uvedeny v levém navigačním podokně na levé straně.
* Opraveno: v podokně akce se někdy zobrazují prázdné akce.
* Opraveno: velikost okna z poslední uzavřené relace je teď zachovaná.
* Opraveno: pomocí místní nabídky můžete otevřít několik karet pro stejný prostředek.

#### <a name="known-issues"></a>Známé problémy

* Rychlý přístup funguje jenom s položkami na základě předplatného. Místní prostředky nebo prostředky připojené pomocí klíče nebo tokenu SAS nejsou v této verzi podporované.
* V závislosti na tom, kolik prostředků máte, může rychlý přístup k cílovému prostředku přejít na několik sekund.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se nahrávají najednou, může dojít k chybám.
* Vyhledávání zpracovává hledání po přibližně 50 000 uzlech – v takovém případě může dojít k ovlivnění výkonu nebo může způsobit neošetřenou výjimku.
* Při prvním použití Průzkumník služby Storage v macOS se může zobrazit více výzev s výzvou k přístupu uživatele k řetězci klíčů. Doporučujeme vybrat možnost vždy umožnit, aby se výzva znovu nezobrazovala.

11/18/2016
### <a name="version-086"></a>0.8.6 verze

#### <a name="new"></a>Nová

* Teď můžete k rychlé navigaci připnout nejčastěji používané služby na rychlý přístup.
* Nyní můžete otevřít více editorů na různých kartách. Jedním kliknutím otevřete dočasnou kartu; dvojím kliknutím otevřete trvalou kartu. Můžete také kliknout na dočasnou kartu a nastavit ji jako trvalou kartu.
* Provedli jsme znatelný výkon a vylepšení stability pro nahrávání a stahování, zejména pro velké soubory na rychlých počítačích.
* V kontejnerech objektů BLOB se teď dají vytvářet prázdné složky Virtual.
* Provedli jsme opětovné uvedení oboru hledání s využitím našeho nového rozšířeného vyhledávání dílčího řetězce, takže teď máte dvě možnosti hledání:
    * Globální vyhledávání – stačí zadat hledaný výraz do textového pole hledání.
    * Vymezené vyhledávání – klikněte na ikonu lupy vedle uzlu, pak přidejte hledaný termín na konec cesty, nebo klikněte pravým tlačítkem a vyberte Hledat odsud.
* Přidali jsme různé motivy: světlá (výchozí), tmavě, Vysoký kontrastová, černá a Vysoký kontrast bílá. Pokud chcete &gt; změnit svoje předvolby, přejděte na Upravit – motivy.
* Můžete upravit vlastnosti objektů BLOB a souborů
* Nyní podporujeme kódované zprávy ve frontě (Base64) a nekódované fronty.
* V systému Linux se nyní vyžaduje 64 operační systém. V této verzi podporujeme jenom 64-bit Ubuntu 16.04.1 LTS
* Aktualizovali jsme logo.

#### <a name="fixes"></a>Opravy

* Opraveno: problémy mrznutí obrazovky
* Opraveno: rozšířené zabezpečení
* Opraveno: někdy se můžou zobrazit duplicitní připojené účty.
* Opraveno: objekt BLOB s nedefinovaným typem obsahu může generovat výjimku.
* Opraveno: otevření panelu dotazů v prázdné tabulce nebylo možné.
* Opraveno: v hledání se liší chyby.
* Opraveno: zvýšení počtu prostředků načtených z 50 na 100 při kliknutí na načíst více
* Opraveno: při prvním spuštění, pokud je účet přihlášený, teď pro tento účet vybereme ve výchozím nastavení všechna předplatná.

#### <a name="known-issues"></a>Známé problémy

* Tato verze Průzkumník služby Storage neběží na Ubuntu 14,04
* Pro otevření více karet pro stejný prostředek neklepejte nepřetržitě na stejný prostředek. Klikněte na jiný prostředek, vraťte se zpátky a potom kliknutím na původní prostředek ho znovu otevřete na jiné kartě.
* Rychlý přístup funguje jenom s položkami na základě předplatného. Místní prostředky nebo prostředky připojené pomocí klíče nebo tokenu SAS nejsou v této verzi podporované.
* V závislosti na tom, kolik prostředků máte, může rychlý přístup k cílovému prostředku přejít na několik sekund.
* Pokud máte víc než 3 skupiny objektů BLOB nebo souborů, které se nahrávají najednou, může dojít k chybám.
* Vyhledávání zpracovává hledání po přibližně 50 000 uzlech – v takovém případě může dojít k ovlivnění výkonu nebo může způsobit neošetřenou výjimku.

03/10/2016
### <a name="version-085"></a>0.8.5 verze

#### <a name="new"></a>Nová

* Teď můžou k připojení k účtům a prostředkům úložiště používat klíče SAS generované portálem.

#### <a name="fixes"></a>Opravy

* Opraveno: podmínka časování při hledání někdy způsobila, že uzly mohou být nerozbalitelné
* Opraveno: při připojování k účtům úložiště s názvem účtu a klíčem nefunguje "použít protokol HTTP".
* Opraveno: klíče SAS (speciálně generované portálem) vrátí chybu "koncového lomítka".
* Opraveno: problémy s importem tabulky
    * Klíč oddílu a klíč řádku se někdy vrátily zpět.
    * Nelze číst klíče oddílů s hodnotou null.

#### <a name="known-issues"></a>Známé problémy

* Vyhledávání zpracovává hledání po zhruba 50 000 uzlech – může to mít vliv na výkon.
* Azure Stack v současné době nepodporuje soubory. při pokusu o rozbalení souborů se zobrazí chyba.

09/12/2016
### <a name="version-084"></a>0.8.4 verze

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nová

* Generování přímých odkazů na účty úložiště, kontejnery, fronty, tabulky nebo sdílené složky pro sdílení a snadný přístup k vašim prostředkům – podpora pro Windows a Mac OS
* Ve vyhledávacím poli můžete hledat kontejnery objektů blob, tabulky, fronty, sdílené složky nebo účty úložiště.
* V Tvůrci dotazů v tabulce teď můžete seskupit klauzule.
* Přejmenování a zkopírování/vložení kontejnerů objektů blob, sdílených složek, tabulek, objektů blob, složek objektů blob, souborů a adresářů v rámci účtů a kontejnerů připojených k SAS
* Přejmenování a zkopírování kontejnerů objektů BLOB a sdílených složek teď zachovávají vlastnosti a metadata

#### <a name="fixes"></a>Opravy

* Opraveno: entity tabulky nelze upravovat, pokud obsahují logické nebo binární vlastnosti

#### <a name="known-issues"></a>Známé problémy

* Vyhledávání zpracovává hledání po zhruba 50 000 uzlech – může to mít vliv na výkon.

08/03/2016
### <a name="version-083"></a>0.8.3 verze

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nová

* Přejmenování kontejnerů, tabulek, sdílených složek
* Vylepšené možnosti Tvůrce dotazů
* Možnost ukládat a načítat dotazy
* Přímé odkazy na účty úložiště nebo kontejnery, fronty, tabulky nebo sdílené složky pro sdílení a snadný přístup k prostředkům (podpora pouze pro Windows připravujeme)
* Možnost spravovat a konfigurovat pravidla CORS

#### <a name="fixes"></a>Opravy

* Opraveno: účty Microsoft vyžadují opakované ověření každých 8-12 hodin.

#### <a name="known-issues"></a>Známé problémy

* Někdy se může zdát, že je uživatelské rozhraní zmrazené – maximalizuje okno a pomůže vám tento problém vyřešit.
* instalace macOS může vyžadovat zvýšená oprávnění.
* Panel nastavení účtu může ukázat, že je potřeba znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná.
* Přejmenování sdílených složek, kontejnerů objektů BLOB a tabulek nezachovává metadata nebo jiné vlastnosti kontejneru, jako je například kvóta sdílení souborů, úroveň veřejného přístupu nebo zásady přístupu.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř přejmenovaného kontejneru objektů BLOB) nezachovává snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity se při přejmenování zachovají.
* Kopírování nebo přejmenování prostředků nefunguje v rámci účtů připojených k SAS

07/07/2016
### <a name="version-082"></a>0.8.2 verze

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nová

* Účty úložiště se seskupují podle předplatných; v uzlu (místní a připojené) se zobrazuje vývojové úložiště a prostředky připojené pomocí klíče nebo SAS.
* Odhlášení z účtů na panelu "nastavení účtu Azure"
* Konfigurace nastavení proxy serveru pro povolení a správu přihlášení
* Vytvoření a přerušení zapůjčení objektů BLOB
* Otevření kontejnerů objektů blob, front, tabulek a souborů jedním kliknutím

#### <a name="fixes"></a>Opravy

* Opraveno: zprávy ve frontě vložené do knihoven .NET nebo Java nejsou správně dekódované z formátu base64.
* Opraveno: $metrics tabulek nejsou zobrazeny pro účty Blob Storage
* Opraveno: uzel Tables nefunguje pro místní (vývojové) úložiště.

#### <a name="known-issues"></a>Známé problémy

* instalace macOS může vyžadovat zvýšená oprávnění.

06/15/2016
### <a name="version-080"></a>0.8.0 verze

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nová

* Podpora sdílení souborů: zobrazování, odesílání, stahování, kopírování souborů a adresářů, identifikátorů URI SAS (vytvoření a připojení)
* Vylepšené uživatelské prostředí pro připojení k úložišti pomocí identifikátorů URI SAS nebo klíčů účtu
* Exportovat výsledky dotazu tabulky
* Změna pořadí a přizpůsobení sloupce tabulky
* Zobrazení $logs kontejnerů objektů BLOB a $metrics tabulek pro účty úložiště s povolenými metrikami
* Vylepšené chování při exportu a importu teď zahrnuje typ hodnoty vlastnosti.

#### <a name="fixes"></a>Opravy

* Opraveno: nahrávání nebo stahování velkých objektů BLOB může mít za následek nekompletní nahrávání/stahování.
* Opraveno: úpravy, přidání nebo import entity s číselnou hodnotou řetězce ("1") ji převede na dvojitou hodnotu.
* Opraveno: nejde rozšířit uzel tabulky v místním vývojovém prostředí.

#### <a name="known-issues"></a>Známé problémy

* $metrics tabulek nejsou viditelné pro účty Blob Storage
* Zprávy fronty přidané programově se nemusí zobrazit správně, pokud jsou zprávy kódované pomocí kódování Base64.

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0 verze

#### <a name="new"></a>Nová

* Lepší zpracování chyb při selhání aplikace

#### <a name="fixes"></a>Opravy

* Opravená chyba, kdy se zprávy informačního panelu někdy nezobrazují, když se vyžadují přihlašovací údaje pro přihlášení

#### <a name="known-issues"></a>Známé problémy

* Tabulky: Přidání, úpravy nebo import entity, která má vlastnost s nejednoznačnou číselnou hodnotou, jako je "1" nebo "1,0" a uživatel se pokusí odeslat jako `Edm.String` , hodnota se vrátí prostřednictvím klientského rozhraní API jako EDM. Double

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0 verze

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nová

* Podpora tabulek: zobrazení, dotazování, export, import a operace CRUD pro entity
* Podpora fronty: zobrazování, přidávání a deřazování zpráv do fronty
* Generování identifikátorů URI SAS pro účty úložiště
* Připojení k účtům úložiště pomocí identifikátorů URI SAS
* Aktualizace oznámení pro budoucí aktualizace Průzkumník služby Storage
* Aktualizovaný vzhled a chování

#### <a name="fixes"></a>Opravy

* Vylepšení výkonu a spolehlivosti

### <a name="known-issues-amp-mitigations"></a>Rizika se známými problémy &amp;

* Stažení velkých souborů BLOB nefunguje správně – při řešení tohoto problému doporučujeme použít AzCopy.
* Přihlašovací údaje účtu se nenačte ani neuloží do mezipaměti, pokud se domovská složka nedá najít nebo do ní nejde zapisovat.
* Pokud přidáváte, upravujete nebo importujete entitu, která má vlastnost s nejednoznačnou číselnou hodnotou, například "1" nebo "1,0" a uživatel se pokusí odeslat jako `Edm.String` , hodnota se vrátí přes klientské rozhraní API jako EDM. Double
* Při importu souborů CSV s víceřádkovými záznamy se můžou data zobrazit roztrhané nebo zakódovat.

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 verze

#### <a name="fixes"></a>Opravy

* Zvýšení celkového výkonu při nahrávání, stahování a kopírování objektů BLOB

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0 verze

#### <a name="new"></a>Nová

* Podpora pro Linux (funkce parity až OSX)
* Přidání kontejnerů objektů BLOB s klíčem sdíleného přístupového podpisu (SAS)
* Přidání účtů úložiště pro Azure Čína 21Vianet
* Přidání účtů úložiště s vlastními koncovými body
* Otevření a zobrazení obsahu a objektů BLOB obrázků
* Zobrazení a úprava vlastností a metadat objektu BLOB

#### <a name="fixes"></a>Opravy

* Opraveno: nahrání nebo stažení velkého počtu objektů BLOB (500 +) může někdy způsobit, že aplikace bude mít bílou obrazovku.
* Opraveno: při nastavování úrovně veřejného přístupu kontejneru objektů BLOB se nová hodnota neaktualizuje, dokud znovu nenastavíte fokus na kontejneru. Dialogové okno má také vždy výchozí hodnotu "bez veřejného přístupu" a nikoli skutečnou aktuální hodnotu.
* Lepší celková podpora klávesnicí a usnadnění přístupu a uživatelského rozhraní
* Text historie cest se zalomí, pokud je dlouhý mezerou
* Dialog SAS podporuje ověřování vstupu
* Místní úložiště je dál dostupné i v případě, že vypršela platnost přihlašovacích údajů uživatele.
* Při odstranění otevřeného kontejneru objektů BLOB se zavře Průzkumník objektů blob na pravé straně.

#### <a name="known-issues"></a>Známé problémy

* Instalace pro Linux vyžaduje aktualizaci nebo upgrade verze RSZ – níže je postup upgradu:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0.7.20151116.0 verze

#### <a name="new"></a>Nová

* macOS a verze Windows
* Přihlaste se, abyste si mohli zobrazit účty úložiště – použijte svůj účet organizace, účet Microsoft, 2FA atd.
* Místní úložiště pro vývoj (použijte emulátor úložiště, jenom Windows)
* Podpora zdrojů Azure Resource Manager a klasických prostředků
* Vytváření a odstraňování objektů blob, front nebo tabulek
* Vyhledat konkrétní objekty blob, fronty nebo tabulky
* Prozkoumat obsah kontejnerů objektů BLOB
* Zobrazení a procházení adresářů
* Nahrávání, stahování a odstraňování objektů BLOB a složek
* Zobrazení a úprava vlastností a metadat objektu BLOB
* Vygenerovat klíče SAS
* Správa a vytváření zásad uloženého přístupu (SAP)
* Hledat objekty blob podle předpony
* Přetažením ' n odkládací soubory pro nahrání nebo stažení

#### <a name="known-issues"></a>Známé problémy

* Při nastavování úrovně veřejného přístupu kontejneru objektů BLOB se nová hodnota neaktualizuje, dokud znovu nenastavíte fokus na kontejneru.
* Když otevřete dialogové okno, abyste nastavili úroveň veřejného přístupu, vždycky se jako výchozí zobrazí "žádný veřejný přístup", a ne skutečnou aktuální hodnotu.
* Stažené objekty blob nejde přejmenovat
* Pokud dojde k chybě, položky protokolu aktivit budou někdy při výskytu chyby zablokované ve stavu probíhá a chyba se nezobrazí.
* Někdy při pokusu o nahrání nebo stažení velkého počtu objektů BLOB dojde k chybě nebo zapínání.
* Někdy zrušení operace kopírování nefunguje
* Pokud při vytváření kontejneru (objekt BLOB/fronta/tabulka) zadáte neplatný název a budete pokračovat v vytváření jiného typu kontejneru, nemůžete nastavit fokus na nový typ.
* Nelze vytvořit novou složku nebo přejmenovat složku.




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md