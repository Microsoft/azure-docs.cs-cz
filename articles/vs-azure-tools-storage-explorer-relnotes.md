---
title: Zpráva k vydání verze Microsoft Azure Storage Explorer
description: Zpráva k vydání verze pro Microsoft Azure Storage Explorer
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
ms.openlocfilehash: 0b2ffc00b6c96f2c31a4b711f618e7b87b6f69e0
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482128"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Zpráva k vydání verze Microsoft Azure Storage Explorer

Tento článek obsahuje nejnovější poznámky k verzi pro Průzkumník služby Azure Storage a také poznámky k verzi pro předchozí verze. 

[Průzkumník služby Microsoft Azure Storage](./vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, MacOS a Linux.

Pokud si chcete stáhnout předchozí verze Průzkumník služby Storage, můžete navštívit [stránku vydání](https://github.com/microsoft/AzureStorageExplorer/releases) našeho úložiště GitHub.

## <a name="version-1110"></a>1\.11.0 verze
11/4/2019

### <a name="new"></a>Nová
* Operace pro objekty blob, ADLS Gen2 a Managed Disks používají integrovaný AzCopy. Přesněji řečeno, následující operace se provádějí pomocí AzCopy:
   * Objekty blob
      * Otevřené pro úpravy a nahrávání
      * Nahrávání, včetně přetažení &
      * Stáhnout
      * Kopírovat & vkládání #1249
      * Odstranění
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
* Při kopírování, nahrávání nebo stahování velkých disků se Průzkumník služby Storage někdy nepodaří odvolat přístup k diskům zapojeným do operace. Tato chyba byla opravena. #2048
* Při zobrazení dotazu na klíč oddílu došlo k chybě statistiky tabulky. Tato chyba byla opravena. #1886

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage 1.11.0 nyní vyžaduje koncový bod DFS (například "myaccount.dfs.core.windows.net") pro připojení k kontejnerům ADLS Gen2. Předchozí verze Průzkumník služby Storage vám umožňují použít koncový bod objektu BLOB. Tyto přílohy po upgradu na 1.11.0 už nemusí fungovat. Pokud se setkáte s tímto problémem, znovu se připojte pomocí koncového bodu DFS.
* Číselná nastavení nejsou kontrolována bez ohledu na to, zda leží v platném rozsahu. #2140
* Kopírování kontejnerů objektů BLOB z jednoho účtu úložiště do jiného ve stromovém zobrazení může selhat. Chystáme se problém prozkoumat. #2124
* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="previous-releases"></a>Předchozí verze

* [1.10.1 verze](#version-1101)
* [1.10.0 verze](#version-1100)
* [1.9.0 verze](#version-190)
* [1.8.1 verze](#version-181)
* [1.8.0 verze](#version-180)
* [1.7.0 verze](#version-170)
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

## <a name="version-1101"></a>1\.10.1 verze
9/19/2019

### <a name="hotfix"></a>Instalaci
* Někteří uživatelé narazili na chybu v 1.10.0 při pokusu o zobrazení jejich dat ve svých účtech ADLS 1. generace. Tato chyba zabránila správnému vykreslení panelu Průzkumníka. Tato chyba byla opravena. #1853 #1865

### <a name="new"></a>Nová
* Průzkumník služby Storage má teď vyhrazené uživatelské rozhraní pro nastavení. Můžete k němu přistoupit buď z úpravy, nebo kliknutím na ikonu nastavení (ozubené kolo) na levém panelu nástrojů. Tato funkce je prvním krokem, který zabereme do poskytování nejrůznějších [nastavení požadovaných pro uživatele](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Od tohoto vydání jsou podporovaná následující nastavení:
  * Motiv
  * Proxy server
  * Odhlásit při ukončení #6
  * Povolit přihlášení ke toku kódu zařízení
  * Automaticky aktualizovat #1526
  * Povolit AzCopy
  * AzCopy SAS – Pokud existují další nastavení, které byste chtěli přidat, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) s popisem nastavení, které chcete zobrazit.
* Průzkumník služby Storage teď podporuje Managed Disks. Můžete:
  * Nahrání Prem virtuálního pevného disku na nový disk
  * Stažení disku
  * Kopírování a vkládání disků napříč skupinami prostředků a oblastmi
  * Odstranit disky
  * Vytvoření snímku disku nahrávání, stahování a kopírování disků v různých oblastech využívá AzCopy v10 za účelem.
* Průzkumník služby Storage se teď dají instalovat přes modul snap-in pro Linux. Při instalaci prostřednictvím obchodu s modulem snap-in jsou pro vás nainstalovány všechny závislosti včetně .NET Core! V současné době jsme ověřili, že Průzkumník služby Storage správně funguje na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z obchodu pro jiné Linux distribuce, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu s modulem snap-in najdete v naší [příručce Začínáme](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
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
* Ve stromové struktuře Průzkumníka (levá strana) je možné přejít do stavu, ve kterém by fokus mohl opakovaně přejít na nejvyšší uzel. Tato chyba byla opravena. #1596
* Při správě snímků objektu BLOB by screenreaders nečetl časové razítko přidružené ke snímku. Tato chyba byla opravena. #1202
* Nastavení proxy serveru na macOS nebylo nastaveno v čase, aby ho proces ověřování mohl použít. Tato chyba byla opravena. #1567
* Pokud byl účet úložiště v rámci svrchovaného cloudu připojený pomocí názvu a klíče, AzCopy nebude fungovat. Tato chyba byla opravena. #1544
* Při připojování pomocí připojovacího řetězce Průzkumník služby Storage nyní odstraní koncové mezery. #1387

### <a name="known-issues"></a>Známé problémy
* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.


## <a name="version-1100"></a>1\.10.0 verze
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

    Pokud existují další nastavení, které byste chtěli přidat, [otevřete prosím problém na GitHubu s popisem nastavení, které chcete zobrazit](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Průzkumník služby Storage teď podporuje Managed Disks. Můžete:
    * Nahrání Prem virtuálního pevného disku na nový disk
    * Stažení disku
    * Kopírování a vkládání disků napříč skupinami prostředků a oblastmi
    * Odstranit disky
    * Vytvoření snímku disku

    Nahrávání, stahování a kopírování disků v různých oblastech využívá AzCopy v10 za účelem.
* Průzkumník služby Storage se teď dají instalovat přes modul snap-in pro Linux. Při instalaci prostřednictvím obchodu s modulem snap-in jsou pro vás nainstalovány všechny závislosti včetně .NET Core! V současné době jsme ověřili, že Průzkumník služby Storage správně funguje na Ubuntu a CentOS. Pokud narazíte na problémy s instalací z obchodu pro jiné Linux distribuce, [otevřete prosím problém na GitHubu](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Další informace o instalaci z obchodu s modulem snap-in najdete v naší [příručce Začínáme](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
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
* Ve stromové struktuře Průzkumníka (levá strana) je možné přejít do stavu, ve kterém by fokus mohl opakovaně přejít na nejvyšší uzel. Tato chyba byla opravena. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Při správě snímků objektu BLOB by screenreaders nečetl časové razítko přidružené ke snímku. Tato chyba byla opravena. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Nastavení proxy serveru na macOS nebylo nastaveno v čase, aby ho proces ověřování mohl použít. Tato chyba byla opravena. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Pokud byl účet úložiště v rámci svrchovaného cloudu připojený pomocí názvu a klíče, AzCopy nebude fungovat. Tato chyba byla opravena. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Při připojování pomocí připojovacího řetězce Průzkumník služby Storage nyní odstraní koncové mezery. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Známé problémy

* Nastavení automatické aktualizace zatím neovlivňuje všechny operace v Průzkumníkovi objektů BLOB.
* Funkce spravovaného disku nejsou v Azure Stack podporovány.
* Pokud se odeslání nebo vložení disku nezdaří a před selháním byl vytvořen nový disk, Průzkumník služby Storage disk za vás neodstraní.
* V závislosti na tom, kdy zrušíte nahrávání nebo vložení disku, je možné tento nový disk ponechat v poškozeném stavu. Pokud k tomu dojde, musíte odstranit nový disk nebo ručně zavolat rozhraní API disku, aby se nahradil obsah disku tak, aby se už nepoškodil.
* Při provádění stahování bez AzCopy objektů BLOB se neověřuje algoritmus MD5 pro velké soubory. Důvodem je chyba v sadě SDK pro úložiště. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
   * Spravované disky
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-190"></a>1\.9.0 verze
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
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-181"></a>1\.8.1 verze
5/13/2019

### <a name="hotfixes"></a>Opravy hotfix
* V některých případech nevrátí kliknutí na tlačítko načíst více na úrovni prostředků další stránku prostředků. Tato chyba byla opravena. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* V systému Windows by stažení AzCopy nebylo úspěšné, pokud se stáhl jeden soubor nebo složka a název souboru nebo složky měl znak, který byl pro cestu Windows neplatný. Tato chyba byla opravena. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Ve extrémně vzácných případech při provádění přejmenování sdílené složky nebo přejmenování ve sdílené složce, pokud se kopie pro přejmenování nepovedla nebo pokud se v úložišti nepovedlo potvrdit úspěch kopií pomocí Azure, bylo možné Průzkumník služby Storage odstranit. riginal soubory před dokončením kopírování. Tato chyba byla opravena.

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
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

## <a name="version-180"></a>1\.8.0 verze
5/1/2019

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
* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
   * ADLS Gen2
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Spuštění Průzkumník služby Storage v systému Linux vyžaduje, aby byly nejprve nainstalovány určité závislosti. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) s Průzkumník služby Storage.

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
* Pokud jste dříve povolili statické weby pro účet úložiště ADLS Gen2 a pak je připojili s názvem a klíčem, Průzkumník služby Storage by se nezjistilo, že byl povolen hierarchický obor názvů. Tato chyba byla opravena. #1081
* V editoru objektů BLOB se sestavuje řazení podle dnů uchovávání dat nebo je stav přerušeno. Tato chyba byla opravena. #1106
* Po 1.5.0 Průzkumník služby Storage už nečekat na dokončení kopií na straně serveru před tím, než se nahlásí po přejmenování nebo zkopírování & vložení. Tato chyba byla opravena. #976
* Při použití experimentální funkce AzCopy se příkaz zkopíroval po kliknutí na možnost zkopírovat příkaz do schránky, který se vždycky spustitelný sám na sebe. Všechny příkazy potřebné k ručnímu spuštění přenosu se teď zkopírují. #1079
* Dříve byly objekty blob ADLS Gen2 nedostupné, pokud jste za proxy. Důvodem byla chyba v nové síťové knihovně, kterou používá sada SDK pro úložiště. V 1.7.0 se provedl pokus o zmírnění tohoto problému, ale někteří lidé můžou dál sledovat problémy. Úplná Oprava bude vydána v budoucí aktualizaci. #1090
* V 1.7.0 teď dialog Uložit soubor správně pamatuje poslední umístění, do kterého jste soubor uložili. #16
* Na panelu Vlastnosti se jako typ účtu zobrazuje úroveň SKU účtu úložiště. Tato chyba byla opravena. #654
* V některých případech nebylo možné přerušit zapůjčení objektu blob, a to i v případě, že jste správně zadali název objektu BLOB. Tato chyba byla opravena. #1070

### <a name="known-issues"></a>Známé problémy

* Pokud používáte RBAC, Průzkumník služby Storage vyžaduje pro přístup k prostředkům úložiště některá oprávnění vrstvy správy. Další informace najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Pokus o přístup k objektům blob ADLS Gen2 v případě, že dojde k selhání za proxy serverem.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1\.6.2 verze
1/9/2019

### <a name="hotfixes"></a>Opravy hotfix
* V 1.6.1 se entity přidané do ADLS Gen2 ACL podle identifikátorů ObjectId, které nebyly uživateli přidány jako skupiny. Nyní se jako skupiny přidají jenom skupiny a entity jako například podnikové aplikace andService objekty zabezpečení se přidají jako uživatelé. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Pokud účet úložiště ADLS Gen2 neobsahoval žádné kontejnery a byl připojen s názvem a klíčem, Průzkumník služby Storage by nerozpoznal, že byl účet úložiště ADLS Gen2. Tato chyba byla opravena. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* V 1.6.0 se konflikty při kopírování a vkládání nedotazují na řešení. Místo toho by mohlo dojít ke konfliktu kopírování. Teď budete požádáni o to, jak se má vyřešit. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Z důvodu omezení rozhraní API byla všechna ověření identifikátorů ObjectId v dialogovém okně Spravovat přístup zakázána. K ověření dojde nyní pouze u uživatelských UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně ADLS Gen2 spravovat přístup nelze změnit oprávnění pro skupinu. Tato chyba byla opravena. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidání podpory přetažení do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně Vlastnosti pro ADLS Gen2 soubory a složky někdy postrádá znak "/". Tato chyba byla opravena. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nepovede získat aktuální oprávnění pro ADLS Gen2 kontejner, soubor nebo složku, pak se tato chyba zobrazí v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH ve Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno připojit se teď zobrazí správně, pokud nejsou přihlášeni žádní uživatelé a nebyli připojeni žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládá vlastnosti pro objekty blob, které nejsou a soubory třídy HNS, do kódování hodnoty každé vlastnosti. Výsledkem je nepotřebné kódování hodnot, které obsahují pouze znaky ASCII. Hodnoty se teď zakódují jenom v případě, že obsahují znaky, které nejsou ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů BLOB bez rozhraní HNS by nebylo úspěšné, pokud se použilo SAS a SAS neměl oprávnění ke čtení. Tato chyba byla opravena. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. Tato chyba byla opravena. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by nedošlo k chybě při pokusu o stažení složky z ADLS Gen2 kontejneru objektů blob, pokud v názvu složky byly mezery. Tato chyba byla opravena. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl v 1.6.0 poškozen. Nyní je opraven. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
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
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1\.6.1 verze
12/18/2018

### <a name="hotfixes"></a>Opravy hotfix
* Z důvodu omezení rozhraní API byla všechna ověření identifikátorů ObjectId v dialogovém okně Spravovat přístup zakázána. K ověření dojde nyní pouze u uživatelských UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* V dialogovém okně ADLS Gen2 spravovat přístup nelze změnit oprávnění pro skupinu. Tato chyba byla opravena. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Přidání podpory přetažení do editoru ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Vlastnost URL v dialogovém okně Vlastnosti pro ADLS Gen2 soubory a složky někdy postrádá znak "/". Tato chyba byla opravena. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Pokud se nepovede získat aktuální oprávnění pro ADLS Gen2 kontejner, soubor nebo složku, pak se tato chyba zobrazí v protokolu aktivit. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Dočasná cesta vytvořená pro otevírání souborů byla zkrácena, aby se snížila pravděpodobnost vytvoření cesty, která je delší než MAX_PATH ve Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogové okno připojit se teď zobrazí správně, pokud nejsou přihlášeni žádní uživatelé a nebyli připojeni žádné prostředky. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* V 1.6.0 ukládá vlastnosti pro objekty blob, které nejsou a soubory třídy HNS, do kódování hodnoty každé vlastnosti. Výsledkem je nepotřebné kódování hodnot, které obsahují pouze znaky ASCII. Hodnoty se teď zakódují jenom v případě, že obsahují znaky, které nejsou ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Nahrání složky do kontejneru objektů BLOB bez rozhraní HNS by nebylo úspěšné, pokud se použilo SAS a SAS neměl oprávnění ke čtení. Tato chyba byla opravena. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Zrušení přenosu AzCopy nefungovalo. Tato chyba byla opravena. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy by nedošlo k chybě při pokusu o stažení složky z ADLS Gen2 kontejneru objektů blob, pokud v názvu složky byly mezery. Tato chyba byla opravena. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Editor CosmosDB byl v 1.6.0 poškozen. Nyní je opraven. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
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
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1\.6.0 verze
5\. 12. 2018

### <a name="new"></a>Nová

* Nyní můžete použít Průzkumník služby Storage pro přístup k datům objektu BLOB prostřednictvím [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Pokud jste přihlášeni a Průzkumník služby Storage není možné načíst klíče pro váš účet úložiště, použije se k ověření při interakci s daty token OAuth.
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
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Verze 1.5.0
29 10. 2018

### <a name="new"></a>Nová

* Teď můžete pro nahrávání a stahování objektů BLOB používat [AzCopy v10 za účelem (Preview)](https://github.com/Azure/azure-storage-azcopy) . Chcete-li povolit tuto funkci přejděte do nabídky "Experimentální" a klikněte na "Použití AzCopy pro vylepšené objektů Blob nahrávání a stahování". Když je povoleno, AzCopy se použijí v následujících scénářích:
   * Nahrání složky a soubory do kontejnerů objektů blob, buď pomocí panelu nástrojů nebo přetažení.
   * Stahování složek a souborů, buď pomocí panelu nástrojů nebo místní nabídky.

* Kromě toho při použití nástroje AzCopy:
   * Můžete zkopírovat příkaz AzCopy používaný k provedení převodu do schránky. Jednoduše klikněte na tlačítko "Kopírovat AzCopy příkaz do schránky" v protokolu aktivit.
   * Je potřeba ručně aktualizovat editor objektů blob po nahrání.
   * Nahrávání souborů do připojených objektů BLOB se nepodporuje a soubory VHD se nahrají jako objekty blob stránky a všechny ostatní soubory se nahrají jako objekty blob bloku.
   * Chyby a konflikty, ke kterým dojde během nahrávání nebo stahování, se neprojeví až po dokončení nahrávání nebo stahování.

Podpora pro použití nástroje AzCopy s využitím sdílených složek a konečně, bude dostupná v budoucnu.
* Průzkumník služby Storage je teď používá elektronovým verze 2.0.11.
* Přerušení zapůjčení můžete nyní provést pouze na jeden objekt blob najednou. Kromě toho je nutné zadat název objektu blob zapůjčení, jejichž jsou zásadní. Tato změna byla provedena, aby se snížila pravděpodobnost náhodného přerušení zapůjčení, zejména pro virtuální počítače. #394
* Pokud někdy narazíte na problémy s přihlašováním, můžete teď zkuste resetovat ověřování. Přejděte do nabídky "Nápověda" a klikněte na tlačítko "Obnovit" přístupu k této funkci. #419

### <a name="fix"></a>Napravit

* Po zpětné vazby uživatelů silné výchozí emulátor uzel se znovu zapnout. Můžete přesto přidat další emulátor připojení přes dialogové okno připojit, ale pokud vaše emulátor je nakonfigurován pro použití výchozí porty můžete použít také uzel "Emulátor * výchozí porty" v části "Místní a připojené a ukládání účtů". #669
* Průzkumník služby Storage už vám umožní nastavit hodnoty metadat objektu blob, které mají počáteční ani koncové prázdné znaky. #760
* Tlačítko "Sign In" se vždy povolena na stejné stránkách dialogového okna připojit. V případě potřeby je nyní zakázán. #761
* Rychlý přístup už vygeneruje chybu v konzole když byly přidány žádné položky rychlý přístup.

### <a name="known-issues"></a>Známé problémy

* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v tématu #537.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, můžete k tomuto problému přidat komentář.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Pokud chcete tento problém obejít při nahrávání nebo stahování z kontejneru objektů blob, můžete použít funkci experimentálního AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Verze 1.4.4
10/15/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět na odblokování uživatelů státní správy Azure USA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání číselníky teď používají animace CSS a snížit množství GPU používané Průzkumníka služby Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externího zdroje, například připojení SAS a emulátory, je výrazně Vylepšená. Nyní můžete:
   * Přizpůsobení zobrazovaný název prostředku, který při připojování. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojení k několika místním emulátory pomocí jiné porty. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidání připojených zdrojů na rychlý přístup. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Umožňuje nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem na uzel kontejnery objektů Blob v účtu úložiště.
   * Zobrazení obnovitelné odstranění objektů BLOB v editoru objektů Blob tak, že vyberete "aktivní a odstranit objekty BLOB" v rozevírací nabídce vedle na navigačním panelu.
   * Obnovit obnovitelně odstraněné objekty BLOB.

### <a name="fixes"></a>Opravy
* Akce "Konfigurovat nastavení CORS" již není k dispozici na účty Storage úrovně Premium, protože účty služby Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Je nyní vlastnost sdílený přístupový podpis SAS připojené služby. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je nyní k dispozici pro objekt Blob a úložiště GPV2 účty, které byla připnuta na rychlý přístup. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech Průzkumníka služby Storage selže účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například emulátoru úložiště Azure nebo Azurite, je potřeba ho naslouchání pro připojení na jejich výchozích portů. Průzkumník služby Storage v opačném případě nebude schopen připojit se k nim.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Verze 1.4.3
10/11/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní API pro správu prostředků Azure byla vrácena zpět na odblokování uživatelů státní správy Azure USA. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Načítání číselníky teď používají animace CSS a snížit množství GPU používané Průzkumníka služby Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nová
* Přílohy externího zdroje, například připojení SAS a emulátory, je výrazně Vylepšená. Nyní můžete:
   * Přizpůsobení zobrazovaný název prostředku, který při připojování. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojení k několika místním emulátory pomocí jiné porty. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidání připojených zdrojů na rychlý přístup. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Umožňuje nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem na uzel kontejnery objektů Blob v účtu úložiště.
   * Zobrazení obnovitelné odstranění objektů BLOB v editoru objektů Blob tak, že vyberete "aktivní a odstranit objekty BLOB" v rozevírací nabídce vedle na navigačním panelu.
   * Obnovit obnovitelně odstraněné objekty BLOB.

### <a name="fixes"></a>Opravy
* Akce "Konfigurovat nastavení CORS" již není k dispozici na účty Storage úrovně Premium, protože účty služby Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Je nyní vlastnost sdílený přístupový podpis SAS připojené služby. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je nyní k dispozici pro objekt Blob a úložiště GPV2 účty, které byla připnuta na rychlý přístup. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech Průzkumníka služby Storage selže účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například emulátoru úložiště Azure nebo Azurite, je potřeba ho naslouchání pro připojení na jejich výchozích portů. Průzkumník služby Storage v opačném případě nebude schopen připojit se k nim.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Verze 1.4.2
09/24/2018

### <a name="hotfixes"></a>Opravy hotfix
* Pokud chcete přidat podporu pro nové typy účtů Azure Storage, aktualizujte verzi rozhraní API pro správu prostředků Azure na 2018-07-01. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nová
* Přílohy externího zdroje, například připojení SAS a emulátory, je výrazně Vylepšená. Nyní můžete:
   * Přizpůsobení zobrazovaný název prostředku, který při připojování. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojení k několika místním emulátory pomocí jiné porty. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidání připojených zdrojů na rychlý přístup. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Umožňuje nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem na uzel kontejnery objektů Blob v účtu úložiště.
   * Zobrazení obnovitelné odstranění objektů BLOB v editoru objektů Blob tak, že vyberete "aktivní a odstranit objekty BLOB" v rozevírací nabídce vedle na navigačním panelu.
   * Obnovit obnovitelně odstraněné objekty BLOB.

### <a name="fixes"></a>Opravy
* Akce "Konfigurovat nastavení CORS" již není k dispozici na účty Storage úrovně Premium, protože účty služby Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Je nyní vlastnost sdílený přístupový podpis SAS připojené služby. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je nyní k dispozici pro objekt Blob a úložiště GPV2 účty, které byla připnuta na rychlý přístup. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech Průzkumníka služby Storage selže účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například emulátoru úložiště Azure nebo Azurite, je potřeba ho naslouchání pro připojení na jejich výchozích portů. Průzkumník služby Storage v opačném případě nebude schopen připojit se k nim.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Verze 1.4.1
08/28/2018

### <a name="hotfixes"></a>Opravy hotfix
* Při prvním spuštění se nepodařilo vygenerovat klíč používaný k šifrování citlivých dat Průzkumníka služby Storage. To způsobí problémy při použití rychlého přístupu a připojení prostředků. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Pokud váš účet nevyžadoval vícefaktorové ověřování pro jeho domovském tenantovi, ale nebyla pro některé klienty, Průzkumníka služby Storage by nemohl do seznamu. Nyní po přihlášení pomocí uvedený účet, Průzkumníka služby Storage vás vyzve k znovu zadat přihlašovací údaje a proveďte vícefaktorové ověřování. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Průzkumník služby Storage se nepodařilo připojit prostředky z Azure Germany a Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Pokud jste se přihlásili k dva účty, které měly stejnou e-mailovou adresu, Průzkumníka služby Storage selže někdy Chcete-li zobrazit vaše prostředky ve stromovém zobrazení. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na počítačích Windows pomalejší úvodní obrazovka by někdy trvat značné množství času se zobrazí. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Dialogové okno Připojit se zobrazí i v případě, že bylo připojené účty nebo služby. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nová
* Přílohy externího zdroje, například připojení SAS a emulátory, je výrazně Vylepšená. Nyní můžete:
   * Přizpůsobení zobrazovaný název prostředku, který při připojování. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Připojení k několika místním emulátory pomocí jiné porty. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Přidání připojených zdrojů na rychlý přístup. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Průzkumník služby Storage teď podporuje obnovitelné odstranění. Můžete:
   * Umožňuje nakonfigurujte zásadu obnovitelného odstranění kliknutím pravým tlačítkem na uzel kontejnery objektů Blob v účtu úložiště.
   * Zobrazení obnovitelné odstranění objektů BLOB v editoru objektů Blob tak, že vyberete "aktivní a odstranit objekty BLOB" v rozevírací nabídce vedle na navigačním panelu.
   * Obnovit obnovitelně odstraněné objekty BLOB.

### <a name="fixes"></a>Opravy
* Akce "Konfigurovat nastavení CORS" již není k dispozici na účty Storage úrovně Premium, protože účty služby Premium Storage nepodporují CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Je nyní vlastnost sdílený přístupový podpis SAS připojené služby. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akce "Nastavit výchozí úroveň přístupu" je nyní k dispozici pro objekt Blob a úložiště GPV2 účty, které byla připnuta na rychlý přístup. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* V některých případech Průzkumníka služby Storage selže účty klasického úložiště. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například emulátoru úložiště Azure nebo Azurite, je potřeba ho naslouchání pro připojení na jejich výchozích portů. Průzkumník služby Storage v opačném případě nebude schopen připojit se k nim.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Verze 1.3.0
07/09/2018

### <a name="new"></a>Nová
* Přístup k kontejnery $web používají statické webové stránky se teď podporuje. To umožňuje snadno nahrávat a spravovat soubory a složky, které používá váš web. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Na panelu aplikace v systému macOS se znovu uspořádat. Změny zahrnují nabídku souborů, některé změny klíčů místní a několik nových příkazů v nabídce aplikace. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Koncový bod autority pro přihlašování ke službě Azure US státní správu byl změněn na https://login.microsoftonline.us/
* Usnadnění: Při aktivním čtečky obrazovky navigaci pomocí klávesnice nyní pracuje se s tabulkami použitá pro zobrazení položek na pravé straně. Klávesy se šipkami můžete použít k procházení řádků a sloupců, Enter k vyvolání akce výchozí kontext klávesy nabídky otevřete místní nabídku pro položku a Shift nebo ovládací prvek vícenásobného výběru. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Opravy
*  Na některých počítačích byly podřízené procesy trvá příliš dlouho spustit. Pokud by k tomu dojít, se zobrazí chybu "podřízený proces se nepodařilo spustit včas". Čas přidělený pro podřízený proces spustit nyní se zvýšil z 20 na 90 sekund. Pokud jsou stále postižené tímto problémem, komentovat prosím propojené problém Githubu. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Při použití SAS, které oprávnění pro čtení, nebylo možné nahrávání velkých objektů blob. V tomto scénáři se změnila logiku pro nahrávání. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Nastavení úroveň veřejného přístupu pro kontejner odeberte všechny zásady přístupu a naopak. Nyní zásady veřejný přístup nebo přístup se zachová, i když nastavení buď z nich. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* V dialogovém okně Vlastnosti byl zkrácen "AccessTierChangeTime". Tato chyba byla opravena. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* "Microsoft Azure Storage Explorer-" předponu nebyl nalezen v dialogovém okně vytvořte nový adresář. Tato chyba byla opravena. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Usnadnění: Dialogové okno Přidat entitu bylo obtížné přejděte při použití VoiceOver. Vylepšili jsme. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Usnadnění: Barva pozadí tlačítka pro podokna akcí a vlastností pro rozbalení/sbalení byl konzistentní se podobně jako ovládací prvky uživatelského rozhraní v motivu Vysoký kontrast – černá. Barva se změnil. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Usnadnění: V motivu Vysoký kontrast – černá fokus pro používání stylů pro tlačítko "X" v dialogovém okně vlastnosti nebyl viditelný. Tato chyba byla opravena. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Usnadnění: Karty Akce a vlastnosti byly chybí několik aria hodnoty, které je v prostředí čtečky subpar obrazovky. Byl přidán chybějící hodnoty aria. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Usnadnění: Uzly sbalený stromu na levé straně nebyly se danou hodnotu rozbalit aria hodnotu false. Tato chyba byla opravena. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Známé problémy
* Odpojení od prostředku připojeného pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání správnému zobrazení jiných příloh. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Další informace najdete v [tomto problému](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce a pokus o použití při práci s Azure Stack může vést k neočekávaným chybám:
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Verzi 1.2.0
06/12/2018

### <a name="new"></a>Nová
* Pokud Průzkumník služby Storage se nepodaří načíst předplatná z pouze podmnožinu vašich klientů, pak je jakékoliv předplatné na byl úspěšně načten zobrazí spolu s chybovou zprávu speciálně pro klienty, které se nepodařilo. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Na Windows Pokud je aktualizace k dispozici, teď můžete "Aktualizace při zavření". Když se tato možnost vybere, se spustí instalační program pro aktualizaci, po ukončení Průzkumníka služby Storage. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Při prohlížení snímku sdílené složky byl do kontextové nabídky editoru sdílené složky přidán snímek pro obnovení. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Tlačítko Vymazat frontu je teď vždycky zapnuté. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Podpora pro přihlášení ke službě AD FS Azure Stack se znovu zapnout. Azure Stack verzi 1804 nebo vyšší je povinný. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Opravy
* Pokud jste zobrazili snímky pro sdílenou složku jehož názvem byla předponu jiné sdílené složky v rámci stejného účtu úložiště, pak snímků pro sdílené složky by také uvedené. Tento problém byl vyřešen. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Když připojených přes SAS, obnovení souboru ze snímku sdílené složky souboru by dojít k chybě. Tento problém byl vyřešen. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Při prohlížení snímky pro objekt blob, zvýšit úroveň snímku akce byla povolena při byly vybrány základní objekt blob a jeden snímek. Akce je nyní povolena pouze pokud je vybraný jeden snímek. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Pokud jedné úlohy (jako je stahování objektu blob) byla spuštěna a později se nezdařilo, se nebude opakovat, automaticky dokud spuštěna jiná úloha stejného typu. Všechny úlohy nyní automaticky opakovat, bez ohledu na to, kolik úlohy mají zařadit do fronty.
* Editory otevřen pro nově kontejnery objektů blob vytvořené v GPV2 a účty Blob Storage neměl sloupec úroveň přístupu. Tento problém byl vyřešen. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Sloupec úroveň přístupu neobjevila by se někdy, když účet úložiště nebo kontejneru objektů blob byl připojených přes SAS. Sloupec teď vždy zobrazí, ale s prázdnou hodnotou. Pokud není nastavena žádná úroveň přístupu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Nastavení úrovně přístupu objektu blob bloku v nově nahraném byla zakázána. Tento problém byl vyřešen. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Pokud tlačítko "Zachovat kartu otevřít" byl vyvolán pomocí klávesnice, by dojít ke ztrátě fokus klávesnice. Nyní fokus se přesune na kartu, která byla zůstat otevřené. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Pro daný dotaz v editoru dotazů nebyl VoiceOver udávající použitelné popis aktuální operátor. Nyní je více popisné. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Odkazy stránkování pro různé editory nebyly popisný. Nebyly změněny bude srozumitelnější. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* V dialogovém okně Přidat entitu nebyl VoiceOver oznámení, jaké sloupce se input element součástí. Název aktuálního sloupce je nyní zahrnutá v popisu prvku. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Přepínací tlačítka a zaškrtávací políčka neměl viditelné ohraničení, pokud fokus. Tento problém byl vyřešen. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Známé problémy
* Při použití emulátorů, jako je například emulátoru úložiště Azure nebo Azurite, je potřeba ho naslouchání pro připojení na jejich výchozích portů. Průzkumník služby Storage v opačném případě nebude schopen připojit se k nim.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Verze 1.1.0
05/09/2018

### <a name="new"></a>Nová
* Průzkumník služby Storage teď podporuje použití Azurite. Poznámka: připojení k Azurite je pevně nastavená na výchozí koncové body vývoje.
* Průzkumník služby Storage teď podporuje úrovně přístupu pro pouze objektů Blob a účty úložiště GPV2. Další informace o úrovních přístupu najdete [tady](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Čas spuštění se už nevyžaduje při generování SAS.

### <a name="fixes"></a>Opravy
* Načítání předplatných pro státní správu USA účty bylo přerušeno. Tento problém byl vyřešen. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Čas vypršení platnosti zásady přístupu nebyl správně se ukládá. Tento problém byl vyřešen. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Při generování adresy URL SAS pro položky v kontejneru, název položky nebyl se připojit k adrese URL. Tento problém byl vyřešen. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Při vytváření SAS, časy vypršení platnosti, které jsou v minulosti by někdy výchozí hodnotu. Důvodem byla Průzkumníka služby Storage pomocí poslední čas začátku a konce platnosti použita jako výchozí hodnoty. Nyní pokaždé, když otevřete dialogové okno SAS se vygeneruje nová sada výchozích hodnot. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Při kopírování mezi účty úložiště, se vygeneruje SAS 24 hodin. Pokud kopie trval po více než 24 hodin, kopie selže. Zvětšili jsme SAS od poslední 1 týden snížíte pravděpodobnost kopii nedaří zálohovat z důvodu neplatné SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* U některých aktivit kliknutím na "Storno" by vždycky pracovali. Tento problém byl vyřešen. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* U některých aktivit, rychlost přenosu byla chybná. Tento problém byl vyřešen. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* V nabídce Zobrazit pravopis "Předchozí" byla chybná. Je teď správně zadána. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Poslední stránky Instalační služby systému Windows bylo na tlačítko "Další". Se změnil na tlačítko "Dokončit". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Přesun fokusu tabulátorem nebyl viditelný pro tlačítka v dialogových oknech při použití motivu černé hybridní připojení. Teď je zobrazený. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Použití malých a velkých "Automaticky vyřešit" pro akce v protokolu aktivit byla chybná. Nyní je správná. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Při odstraňování entity z tabulky, zobrazí se dialogové okno s výzvou k potvrzení ikona chyby. Dialogové okno nyní používá ikonou upozornění. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud tomu tak není, odblokujte prosím [Tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Verze 1.0.0
04/16/2018

### <a name="new"></a>Nová
* Vylepšené ověřování, který umožňuje Průzkumníka služby Storage pro použití stejného účtu úložiště jako Visual Studio 2017. Pokud chcete používat tuto funkci, je potřeba opětovné přihlášení k účtům a znovu nastavte předplatné filtrované.
* Pro účty služby Azure Stack se opírá o AAD Průzkumníka služby Storage teď načte předplatná Azure Stack po "Target Azure Stack" je povolená. Už je potřeba vytvořit vlastní přihlašovací prostředí.
* Několik zkratky byly přidány k povolení rychlejší navigace. Patří mezi ně při přepínání panelů různých a přesouvání mezi editory. Najdete v nabídce Zobrazit další podrobnosti.
* Storage Explorer zpětné vazby se nyní nachází na Githubu. Na stránku problémů se dostanete tak, že kliknete na tlačítko zpětná vazba v dolní části doleva nebo přejdete na [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). Nebojte se posílat návrhy na vylepšení, hlášení problémů, klást otázky nebo ponechte jakoukoli jinou formu zpětnou vazbu.
* Pokud používáte k problémům s certifikátem SSL a nemůžete najít poškozený certifikát, můžete teď Průzkumník služby Storage spustit z příkazového řádku pomocí příznaku `--ignore-certificate-errors`. Při spuštění s tímto příznakem, Průzkumníka služby Storage se bude ignorovat chyby certifikátů SSL.
* Teď je k dispozici možnost "Stáhnout" v místní nabídce pro objekt blob a souboru položky.
* Lepší dostupnost a podpora čtečky obrazovky. Pokud se spoléháte na funkce přístupnosti, najdete další informace v [dokumentaci k usnadnění](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) .
* Průzkumník služby Storage teď používá elektronovým 1.8.3

### <a name="breaking-changes"></a>Zásadní změny
* Průzkumník služby Storage se přepnulo na novou knihovnu ověřování. V rámci přepínače do knihovny budete muset znovu – přihlášení k účtům a znovu nastavte předplatné filtrované
* Metoda používá k šifrování citlivých dat změněna. To může vést k některé z položek aplikace rychlý přístup by bylo potřeba jej znovu přidat, a/nebo někteří z vás připojené zdroje, které by bylo potřeba lze znovu připojit.

### <a name="fixes"></a>Opravy
* Někteří uživatelé za proxy by mají skupiny objektů blob nahrání nebo stažení přerušila 'Nelze přeložit' chybová zpráva. Tento problém byl vyřešen.
* Podle potřeby přihlášení se při použití přímého odkazu, kliknutím na řádek "Přihlášení" by vyvolat přes pop prázdné okno. Tento problém byl vyřešen.
* V Linuxu, pokud Průzkumník služby Storage se nejde spustit kvůli chybovému ukončení procesu GPU, budete teď informováni selhání některého použít "--Zakázat gpu' přepínače a Průzkumníka služby Storage se pak automaticky vytvářet znovu s přepínačem povolena.
* Neplatný přístup zásady byly obtížné identity v dialogovém okně zásad přístupu. Zásady přístupu neplatné ID jsou nyní červeně pro větší přehled.
* Protokol aktivit by měla mít někdy velké oblasti prázdné znaky mezi různé části aktivity. Tento problém byl vyřešen.
* V editoru dotazů tabulky Pokud left klauzulí timestamp v neplatném stavu a pak se pokusila upravit další klauzuli editoru ukotvením. Editor obnoví v klauzuli timestamp na platný stavu poslední při zjištění změny v jiné klauzuli.
* Pokud se vám pozastaví při psaní do vyhledávacího dotazu ve stromovém zobrazení, vyhledávání začne a fokus by ukradeny z textového pole. Nyní je nutné explicitně spustit hledání stisknutím klávesy "Zadejte" nebo kliknutím na tlačítko start vyhledávání.
* Příkaz "Získat sdílený přístupový podpis" by někdy deaktivuje, když kliknete pravým tlačítkem na soubor ve sdílené složce. Tento problém byl vyřešen.
* Pokud uzel stromu prostředků s fokusem vyfiltrovaný při hledání, nebylo možné kartu do stromu prostředků a pomocí kláves se šipkami přejdete na strom prostředků. Nyní Pokud uzel stromu cílených prostředků je skrytý, první uzel ve stromu prostředků bude automaticky zaměřit.
* Navíc oddělovač někdy staly viditelnými v panelu nástrojů editoru. Tento problém byl vyřešen.
* Do textového pole s popisem cesty v některých případech k přetečení. Tento problém byl vyřešen.
* Editory objektů Blob a sdílenou složku by někdy neustále aktualizovat při nahrání více souborů najednou. Tento problém byl vyřešen.
* V zobrazení Správa snímků sdílené složky souborů došlo k funkci 'Statistiky složky' žádný účel. Je teď zakázaná.
* V Linuxu nenacházely se v nabídce Soubor. Tento problém byl vyřešen.
* Při nahrávání složky do sdílené složky, ve výchozím nastavení, pouze obsah složky nahraný. Výchozí chování je nyní, odešlete obsah složky do odpovídající složky ve sdílené složce.
* Řazení tlačítka v dialogových oknech několik měl obrácený. Tento problém byl vyřešen.
* Oprav souvisejících s různými zabezpečení.

### <a name="known-issues"></a>Známé problémy
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele se systémem Linux bude nutné nainstalovat [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Verze 0.9.6
02/28/2018

### <a name="fixes"></a>Opravy
* Problém zabránily očekávané soubory a objekty BLOB zobrazovaly v editoru. Tento problém byl vyřešen.
* Problém způsobil, přepínání mezi zobrazeními snímku pro zobrazení položek nesprávně. Tento problém byl vyřešen.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme [popsanou práci](https://github.com/Azure/azure-storage-node/issues/317)filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Verze 0.9.5
02/06/2018

### <a name="new"></a>Nová

* Podpora pro snímky sdílené složky:
    * Vytvoření a Správa snímků sdílených složek.
    * Když se budete učit snadno přepněte zobrazení mezi snímky sdílené složky.
    * Obnovte předchozí verze svých souborů.
* Podpora pro Azure Data Lake Store Preview:
    * Připojení k vašim prostředkům ADLS v několika účtech.
    * K připojení a sdílení prostředků ADLS pomocí identifikátorů URI ADL.
    * Provedení operace rekurzivně základní soubor nebo složku.
    * Připnout jednotlivé složky na rychlý přístup.
    * Zobrazit statistiky složky.

### <a name="fixes"></a>Opravy
* Vylepšení výkonu při spuštění.
* Různé opravy chyb.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Verze 0.9.4 a 0.9.3
21/01/2018

### <a name="new"></a>Nová
* Existující okno Průzkumníka služby Storage bude znovu použít při:
    * Otevřením přímé odkazy, které jsou generovány v Průzkumníku služby Storage.
    * Otevření Průzkumníka služby Storage z portálu.
    * Otevření Průzkumníka služby Storage z Azure Storage VS Code příponou (už brzo).
* Přidání možnosti otevřete nové okno Průzkumníka služby Storage z v Průzkumníkovi služby Storage.
    * Pro Windows je možnost 'Nové okno' v nabídce Soubor a v místní nabídce na hlavním panelu.
    * Pro Mac je možnost 'Nové okno' v nabídce aplikace.

### <a name="fixes"></a>Opravy
* Opravili jsme chybu zabezpečení. Upgradujte prosím 0.9.4 nejdříve.
* Staré aktivity nebyly správně právě prochází čištěním. To vliv na výkon dlouhodobě spuštěných úloh. Tyto jsou teď právě prochází čištěním správně.
* Akce zahrnující velké množství souborů a adresářů občas způsobí Průzkumníka služby Storage zablokovat a nešlo. Požadavky na Azure pro sdílené složky se nyní omezují k omezení využití prostředků v systému.

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Průzkumník" a "Správa účtů zobrazení" by měl být Ctrl / Cmd + Shift + E a Ctrl / Cmd + Shift + A v uvedeném pořadí.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Verze 0.9.2
11/01/2017

### <a name="hotfixes"></a>Opravy hotfix
* Neočekávaná data změny nebyly podporované, při úpravě hodnoty Edm.DateTime pro tabulkové entity v závislosti na místní časové pásmo. Editor teď používá pole ve formátu prostého textu, poskytuje přesné a konzistentní kontrolu nad Edm.DateTime hodnoty.
* Nahrání/stažení skupiny objektů BLOB při připojení s názvem a klíčem se nespustí. Tento problém byl vyřešen.
* Dříve Průzkumníka služby Storage by pouze vyzve k donutit starý účet, pokud jeden nebo několik předplatných na účet byl vybrán. Nyní Storage Explorer vyzve i v případě, že účet je plně odfiltrována.
* Doména koncových bodů pro Azure US Government byla chybná. Chyba byla opravena.
* Použít tlačítko na panelu Spravovat účty se někdy obtížně klikněte na tlačítko. To by měl nebudou probíhat.

### <a name="new"></a>Nová
* Podpora verze Preview pro službu Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytvoření databáze a kolekce
    * Pracovat s daty
    * Dotazování, vytváření a odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelsky definovaných funkcí nebo triggerů
    * Použít připojovací řetězce k připojení a spravovat databáze
* Vyšší výkon nahrávání a stahování mnoha malých objektů BLOB.
* Přidali "Opakujte vše" akci při selhání ve skupině nahrání objektu blob nebo skupina pro stažení objektů blob.
* Průzkumník služby Storage se nyní pozastavit iteraci během nahrávání a stahování objektů blob, jestliže zjistí, že bylo ztraceno připojení k síti. Iterace můžete poté obnovit, jakmile znovu naváže připojení k síti.
* Přidali jsme možnost "Zavřít vše", "Zavřít ostatní" a "Zavřít" karty pomocí místní nabídky.
* Průzkumník služby Storage teď používá nativní dialogových oken a nativní kontextové nabídky.
* Průzkumník služby Storage je teď mnohem přístupnější. Mezi vylepšení patří:
    * Vylepšené podpoře čtečky obrazovky, NVDA na Windows a VoiceOver na počítači Mac
    * Vylepšené motiv s vysokým kontrastem
    * Opravy tabulátor a klávesnice fokus klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt blob se neplatný název souboru Windows, operace selže. Průzkumník služby Storage se nyní zjistí, jestli název objektu blob je neplatný a požádat, pokud chcete jeho kódování nebo Přeskočit tento objekt blob. Průzkumník služby Storage taky bude zjišťovat, pokud název souboru se zdá být zakódován a dotaz, pokud chcete dekódovat před odesláním.
* Při odeslání objektu blob editor pro cílový kontejner objektu blob nebude někdy aktualizovat správně. Tento problém byl vyřešen.
* Který poklesl podporu pro několik tvarů připojovací řetězce a identifikátorů URI SAS. Jsme vyřešili všechny známé problémy, ale pokud budete mít dál problémy, pošlete zpětnou vazbu.
* Oznámení o aktualizaci pro některé uživatele ve 0.9.0 poškozený. Tento problém byl vyřešen a pro ty, kterých se tato chyba týká, můžete ručně stáhnout nejnovější [verzi Průzkumník služby Storage.](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Průzkumník" a "Správa účtů zobrazení" by měl být Ctrl / Cmd + Shift + E a Ctrl / Cmd + Shift + A v uvedeném pořadí.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Verze 0.9.1 a 0.9.0
10/20/2017
### <a name="new"></a>Nová
* Podpora verze Preview pro službu Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytvoření databáze a kolekce
    * Pracovat s daty
    * Dotazování, vytváření a odstraňování dokumentů
    * Aktualizace uložených procedur, uživatelsky definovaných funkcí nebo triggerů
    * Použít připojovací řetězce k připojení a spravovat databáze
* Vyšší výkon nahrávání a stahování mnoha malých objektů BLOB.
* Přidali "Opakujte vše" akci při selhání ve skupině nahrání objektu blob nebo skupina pro stažení objektů blob.
* Průzkumník služby Storage se nyní pozastavit iteraci během nahrávání a stahování objektů blob, jestliže zjistí, že bylo ztraceno připojení k síti. Iterace můžete poté obnovit, jakmile znovu naváže připojení k síti.
* Přidali jsme možnost "Zavřít vše", "Zavřít ostatní" a "Zavřít" karty pomocí místní nabídky.
* Průzkumník služby Storage teď používá nativní dialogových oken a nativní kontextové nabídky.
* Průzkumník služby Storage je teď mnohem přístupnější. Mezi vylepšení patří:
    * Vylepšené podpoře čtečky obrazovky, NVDA na Windows a VoiceOver na počítači Mac
    * Vylepšené motiv s vysokým kontrastem
    * Opravy tabulátor a klávesnice fokus klávesnice

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stáhnout objekt blob se neplatný název souboru Windows, operace selže. Průzkumník služby Storage se nyní zjistí, jestli název objektu blob je neplatný a požádat, pokud chcete jeho kódování nebo Přeskočit tento objekt blob. Průzkumník služby Storage taky bude zjišťovat, pokud název souboru se zdá být zakódován a dotaz, pokud chcete dekódovat před odesláním.
* Při odeslání objektu blob editor pro cílový kontejner objektu blob nebude někdy aktualizovat správně. Tento problém byl vyřešen.
* Který poklesl podporu pro několik tvarů připojovací řetězce a identifikátorů URI SAS. Jsme vyřešili všechny známé problémy, ale pokud budete mít dál problémy, pošlete zpětnou vazbu.
* Oznámení o aktualizaci pro některé uživatele ve 0.9.0 poškozený. Tento problém byl vyřešen a pro ty, kterých se tato chyba týká, můžete ručně stáhnout nejnovější verzi Průzkumník služby Storage [sem](https://azure.microsoft.com/features/storage-explorer/) .

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Průzkumník" a "Správa účtů zobrazení" by měl být Ctrl / Cmd + Shift + E a Ctrl / Cmd + Shift + A v uvedeném pořadí.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že používáme popsanou práci filtru zrušit.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage zobrazuje prázdné hlavní okno (prázdné), můžete zkusit spustit Průzkumník služby Storage z příkazového řádku a zakázat akceleraci GPU přidáním přepínače `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Verze 0.8.16
8/21/2017

### <a name="new"></a>Nová
* Při otevření objektu blob Storage Explorer vyzve k stažený soubor nahrát, pokud je zjištěna změna
* Vylepšené Azure Stack přihlašovací prostředí
* Vyšší výkon nahrávání a stahování mnoha malých souborů ve stejnou dobu


### <a name="fixes"></a>Opravy
* Pro některé typy objektů blob výběrem možnosti "replace" během došlo ke konfliktu nahrávání někdy způsobí nahrávání Probíhá restartování.
* Ve verzi 0.8.15 nahrávání by někdy manipulace blokováním do 99 %.
* Při nahrávání souborů do sdílené složky, pokud jste se rozhodli nahrát do adresáře, který nebyl dosud neexistuje, nahrávání selže.
* Průzkumník služby Storage se nesprávně generování časová razítka pro sdílené přístupové podpisy a dotazy na tabulku.


### <a name="known-issues"></a>Známé problémy
* Pomocí názvu a připojovací řetězec klíče aktuálně nefunguje. Vyřeší v další vydané verzi. Do té doby můžete připojit pomocí názvu a klíče.
* Při pokusu o otevření souboru s neplatným názvem souboru Windows stahování výsledkem bude chyba nenalezení souboru.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Toto je omezení knihovny uzel úložiště Azure.
* Po dokončení odeslání objektu blob, se aktualizují na kartě, která iniciovala nahrávání. To se liší od předchozí chování a také způsobí přejdete zpět do kořenového adresáře, který se v kontejneru.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Pro uživatele v Ubuntu 14.04, budete muset ověřte, že je aktuální GCC – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete udělat spuštěním následujících příkazů a následného restartování počítače:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Verze 0.8.14
06/22/2017

### <a name="new"></a>Nová

* Aktualizovaná verze elektronovým k 1.7.2, abyste mohli využívat několik důležité aktualizace zabezpečení
* Můžete teď rychle získat online Průvodce odstraňováním potíží z nabídky Nápověda
* [Průvodce][2] odstraňováním potíží s Průzkumník služby Storage
* [Pokyny][3] pro připojení k předplatnému Azure Stack

### <a name="known-issues"></a>Známé problémy

* Tlačítka na potvrzovací dialogové okno Odstranit složku nezaregistrujete s myší v Linuxu. Chcete-li použít klávesu ENTER, použijte alternativní postup.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta pak budete muset restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Panel nastavení účtu se může zobrazit, že budete muset znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Ubuntu 14.04 instalace potřebuje verze gcc, aktualizován nebo upgradovat – postup upgradu jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Verze 0.8.13
05/12/2017

#### <a name="new"></a>Nová

* [Průvodce][2] odstraňováním potíží s Průzkumník služby Storage
* [Pokyny][3] pro připojení k předplatnému Azure Stack

#### <a name="fixes"></a>Opravy

* Opraveno: Nahrání souboru využili vysokou příležitost sestavení z důvodu nedostatku paměti
* Opraveno: Můžete teď přihlášení pomocí PIN kódu nebo čipová karta
* Opraveno: otevřít na portálu teď funguje s Azure Čína 21Vianet, Azure Německo, Azure US vládou a Azure Stack
* Opraveno: Při odesílání do složky na kontejner objektů blob, "Neplatná operace" by někdy dojít k chybě
* Opraveno: Při správě snímků byl zakázán Vybrat vše
* Opraveno: Může získat metadata objektu blob základní přepsat po zobrazení vlastností její snímky

#### <a name="known-issues"></a>Známé problémy

* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta pak budete muset restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage
* Při snížení nebo navýšení kapacity v měřítku, úroveň zvětšení může momentálně resetovat na výchozí úroveň
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Panel nastavení účtu se může zobrazit, že budete muset znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Ubuntu 14.04 instalace potřebuje verze gcc, aktualizován nebo upgradovat – postup upgradu jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Verze 0.8.12 a 0.8.11 a 0.8.10
04/07/2017

#### <a name="new"></a>Nová

* Průzkumník služby Storage se teď automaticky zavře po instalaci aktualizace z oznámení o aktualizaci
* Místní rychlý přístup obsahuje vylepšené prostředí pro práci s často používané prostředky
* V editoru kontejner objektů Blob nyní je vidět které virtuální počítač patří zapůjčení objektu blob
* Nyní můžete sbalit panel na levé straně
* Zjišťování se teď spustí ve stejnou dobu jako soubor ke stažení
* Pomocí statistiky v editorech kontejner objektů Blob, sdílené složky a tabulky zobrazíte velikost prostředku nebo výběru
* Můžete teď přihlásit k Azure Active Directory (AAD) na základě účtů služby Azure Stack.
* Teď můžete nahrát soubory archivu více než 32MB do účtů Premium storage
* Vylepšená podpora pro usnadnění
* Kliknutím na Upravit&gt; certifikáty SSL teď můžete přidat certifikáty SSL s kódováním Base-64, které budou&gt; importovat certifikáty.

#### <a name="fixes"></a>Opravy

* Opraveno: po aktualizaci přihlašovacích údajů účtu, ve stromovém zobrazení by někdy aktualizace automaticky
* Opraveno: vygenerování SAS pro emulátor fronty a tabulky výsledkem by byla neplatná adresa URL
* Opraveno: účty služby premium storage teď se rozšířit proxy server je povolen.
* Opraveno: tlačítko použít na stránce účty pro správu nebude fungovat, pokud jste měli 1 nebo 0 účty vybrané
* Opraveno: nahrání objektů BLOB, které vyžadují řešení konfliktů může selhat – v 0.8.11
* Opraveno: odeslání názoru byla porušena v 0.8.11 – opraveno v 0.8.12

#### <a name="known-issues"></a>Známé problémy

* Po upgradu na 0.8.10, je potřeba aktualizovat všechny svoje přihlašovací údaje.
* Zatímco možnosti zvětšit snížení nebo navýšení kapacity, může úroveň zvětšení okamžik obnovíte výchozí úroveň.
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Ubuntu 14.04 instalace potřebuje verze gcc, aktualizován nebo upgradovat – postup upgradu jsou níže:

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

* Průzkumník služby Storage 0.8.9 automaticky stáhne nejnovější aktualizace.
* Oprava hotfix: prostřednictvím portálu vygenerovaný identifikátor URI SAS pro připojení k účtu úložiště by dojít k chybě.
* Nyní můžete vytvářet, spravovat a zvýšení úrovně se snímky objektů blob.
* Nyní se můžete přihlásit ke službě Azure Čína 21Vianet, Azure Německo a Azure pro státní správu USA.
* Nyní můžete změnit úroveň zvětšení. Pomocí možností v nabídce Zobrazit přiblížit, oddálit a resetovat zvětšení.
* Znaky Unicode jsou nyní podporovány uživatele metadat pro objekty BLOB a soubory.
* Vylepšení přístupnosti.
* Zpráva k vydání verze pro další verzi zobrazením z oznámení o aktualizaci. Můžete také zobrazit aktuální zpráva k vydání verze v nabídce Nápověda.

#### <a name="fixes"></a>Opravy

* Opraveno: číslo verze je nyní správně zobrazuje v Ovládacích panelech na Windows
* Opraveno: vyhledávání už není omezené na 50 000 uzly
* Opraveno: nahrát do sdílené složky podařilo navždy, pokud cílový adresář již neexistuje
* Opraveno: vylepšení stability pro dlouhé nahrávání a stahování

#### <a name="known-issues"></a>Známé problémy

* Zatímco možnosti zvětšit snížení nebo navýšení kapacity, může úroveň zvětšení okamžik obnovíte výchozí úroveň.
* Rychlý přístup funguje jenom s položkami na základě předplatného. V této verzi nejsou podporovány místním prostředkům nebo prostředkům připojeným prostřednictvím klíč nebo SAS token.
* Za několik sekund, přejděte na cílový prostředek, v závislosti na tom, kolik prostředků, které máte, může trvat rychlý přístup.
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby.

12/16/2016
### <a name="version-087"></a>Verze 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nová

* Můžete zvolit způsob řešení konfliktů na začátku aktualizace, stahování nebo kopírování relace v okně aktivity
* Najeďte myší na kartě zobrazíte úplnou cestu k prostředku úložiště
* Když kliknete na kartu, synchronizuje s jeho umístění v navigačním podokně na levé straně

#### <a name="fixes"></a>Opravy

* Opraveno: Průzkumník služby Storage je teď důvěryhodné aplikace na počítači Mac
* Opraveno: Ubuntu 14.04 opět je podporována
* Opraveno: v některých případech přidat účet uživatelského rozhraní bliká při načítání předplatných
* Opraveno: Někdy všechny prostředky úložiště byly uvedené v navigačním podokně na levé straně
* Opraveno: V podokně Akce někdy zobrazí prázdné akce
* Opraveno: Velikost okna z poslední relace uzavřené se nyní uchovávají
* Opraveno: Můžete otevřít více karet pro stejný prostředek pomocí místní nabídky

#### <a name="known-issues"></a>Známé problémy

* Rychlý přístup funguje jenom s položkami na základě předplatného. V této verzi nepodporuje místním prostředkům nebo prostředkům připojeným prostřednictvím klíč nebo SAS token
* Může trvat rychlý přístup a přejděte do cílového prostředku, v závislosti na tom, kolik prostředků máte během několika sekund
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Hledání popisovače hledání v rámci přibližně 50 000 uzlů – za to, výkonu může mít vliv nebo může způsobit neošetřená výjimka
* Poprvé v systému macOS pomocí Storage Explorer může se zobrazit více výzev, které žádá o oprávnění uživatele pro přístup k řetězci klíčů. Doporučujeme že vybrat vždy povolit tak zobrazení výzvy znovu nezobrazí

11/18/2016
### <a name="version-086"></a>Verze 0.8.6

#### <a name="new"></a>Nová

* Můžete teď připnout nejčastěji používané služby na rychlý přístup pro snadnou navigaci
* Teď můžete otevřít více editory na různých záložkách. Jedním kliknutím otevřete dočasnou kartu; dvojím kliknutím otevřete trvalou kartu. Můžete také kliknout na dočasnou kartu a nastavit ji jako trvalou kartu.
* Vylepšili jsme zřetelný rozdíl ve výkonu a vylepšení stability pro odesílání a stahování souborů, zejména u velkých souborů na rychlé počítače
* Prázdné složky "virtuální" teď je možné vytvářet v kontejnerech objektů blob
* Znovu zavedli jsme cíleně prohledejte s naší nové hledání dílčího řetězce rozšířené, takže Teď máte dvě možnosti pro hledání:
    * Globální vyhledávání – stačí do textového pole hledání zadejte hledaný termín
    * Cíleně prohledejte – klikněte na ikonu lupy vedle uzlu, pak přidat hledaný termín do konce cesty, nebo klikněte pravým tlačítkem a vyberte "Vyhledávání z tady"
* Přidali jsme různé motivy: světla (výchozí), tmavý, vysoký kontrast – černá a Vysoký kontrast – bílá. Pokud chcete změnit předvolby pro&gt;, přejděte na motivy úprav-.
* Můžete upravit vlastnosti objektů Blob a souborů
* Nyní podporujeme kódovaný (base64) a nekódovaného jejich zprávy.
* V Linuxu, OS 64-bit nyní je povinný. Pro tuto verzi se podporuje jenom 64bitová verze Ubuntu 16.04.1 LTS
* Aktualizovali jsme naším logem!

#### <a name="fixes"></a>Opravy

* Opraveno: Obrazovky zmrazení problémy
* Opraveno: Rozšířené zabezpečení
* Opraveno: Může zobrazovat někdy duplicitní připojené účty
* Opraveno: Objekt blob se Nedefinovaný typ obsahu může generovat výjimky
* Opraveno: Otevření panelu dotazu na prázdnou tabulku nebylo možné
* Opraveno: Chyby při hledání se liší
* Opraveno: Zvýšení počtu prostředků načtena z 50 až 100 při kliknutí na "Načíst další"
* Opraveno: Při prvním spuštění, pokud je přihlášení účtu, teď vybereme Všechna předplatná pro tento účet ve výchozím nastavení

#### <a name="known-issues"></a>Známé problémy

* Tuto verzi Storage Exploreru neběží v Ubuntu 14.04
* Chcete-li spustit více karet pro jeden prostředek, neklikejte na neustále se stejným prostředkem. Klikněte na jiný prostředek a potom přejděte zpět a potom klikněte na původní prostředek, který chcete znovu otevřít na jiné kartě
* Rychlý přístup funguje jenom s položkami na základě předplatného. V této verzi nepodporuje místním prostředkům nebo prostředkům připojeným prostřednictvím klíč nebo SAS token
* Může trvat rychlý přístup a přejděte do cílového prostředku, v závislosti na tom, kolik prostředků máte během několika sekund
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Hledání popisovače hledání v rámci přibližně 50 000 uzlů – za to, výkonu může mít vliv nebo může způsobit neošetřená výjimka

10/03/2016
### <a name="version-085"></a>Verze 0.8.5

#### <a name="new"></a>Nová

* Teď můžete pomocí klíče portálu vygeneruje SAS připojit k účtům úložiště a prostředků

#### <a name="fixes"></a>Opravy

* Opraveno: způsobila konflikt časování během vyhledávání někdy uzly stát bez rozšíření
* Opraveno: "Použít protokol HTTP" nefunguje při připojování k účtům úložiště pomocí názvu a klíče účtu
* Opraveno: Klíče SAS (ty speciálně portálu vygeneruje) vrátí chybu "koncové lomítko"
* Opraveno: importovat tabulku problémy
    * Někdy byly vráceny zpět klíče oddílu a klíč řádku
    * Nelze číst "null" klíči oddílů

#### <a name="known-issues"></a>Známé problémy

* Hledání popisovače hledání v rámci přibližně 50 000 uzlů – za to, může mít dopad na výkon
* Azure Stack v současné době nepodporuje soubory, takže pokusu o rozbalení souborů se zobrazí chyba

09/12/2016
### <a name="version-084"></a>Verze 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nová

* Generovat přímé odkazy na účty úložiště, kontejnery, front, tabulek nebo sdílené složky pro sdílení a podporu snadného přístupu k vašim prostředkům – Windows a Mac OS
* Hledat kontejnery objektů blob, tabulky, fronty, sdílené složky nebo účty úložiště z vyhledávacího pole
* Nyní můžete seskupit klauzule v tabulce Tvůrce dotazů
* Přejmenovat a kopírování/vkládání kontejnery objektů blob, sdílené složky, tabulky, objekty BLOB, složky objektů blob, souborů a adresářů z v rámci účty připojené sdíleným přístupovým Podpisem a kontejnery
* Přejmenování a kopírování kontejnery objektů blob a sdílené složky teď zachovat vlastností a metadat

#### <a name="fixes"></a>Opravy

* Opraveno: nelze upravit tabulkové entity, pokud obsahují logická nebo binární vlastnosti

#### <a name="known-issues"></a>Známé problémy

* Hledání popisovače hledání v rámci přibližně 50 000 uzlů – za to, může mít dopad na výkon

08/03/2016
### <a name="version-083"></a>Verze 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nová

* Přejmenovat kontejnerů, tabulek, sdílené složky
* Vylepšené prostředí pro tvůrce dotazů
* Možnost uložení a načtení dotazů
* Přímé odkazy na úložiště účtů nebo kontejnery, front, tabulek, nebo sdílené složky pro sdílení a snadného přístupu k prostředkům (pouze Windows - macOS podporu Připravujeme!)
* Schopnost spravovat a konfigurovat pravidla CORS

#### <a name="fixes"></a>Opravy

* Opraveno: Microsoft Accounts vyžadovat opakované ověření každých 8 až 12 hodin

#### <a name="known-issues"></a>Známé problémy

* Někdy uživatelské rozhraní může vypadat zmrazené – maximalizovat okno pomáhá řešit tento problém
* macOS instalace může vyžadovat zvýšenou úroveň oprávnění
* Panel nastavení účtu se může zobrazit, že budete muset znovu zadat přihlašovací údaje, aby bylo možné filtrovat předplatná
* Přejmenování sdílené, kontejnerů objektů blob a tabulek nezachová metadat nebo jiné vlastnosti v kontejneru, jako je například kvóta pro sdílenou složku, úroveň veřejného přístupu nebo zásad přístupu
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadata pro objekty blob, soubory a entity se při přejmenování zachovají.
* Kopírování nebo přejmenování zdroje nefunguje v rámci účty připojené sdíleným přístupovým Podpisem

07/07/2016
### <a name="version-082"></a>Verze 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nová

* Účty úložiště jsou seskupené podle předplatných; vývojovým úložištěm a prostředkům připojeným prostřednictvím klíč nebo SAS se zobrazí v uzlu (místní a připojené)
* Odhlášení z účtů na panelu "nastavení účtu Azure"
* Konfigurace nastavení proxy serveru povolit a spravovat přihlášení
* Vytvoření a zrušit zapůjčení objektu blob
* Kontejnery otevřít objekt blob, fronty, tabulky a soubory s jedním kliknutím

#### <a name="fixes"></a>Opravy

* Opraveno: zprávy do fronty Vložit s knihovnami .NET nebo Javě nejsou dekódovat správně z formátu base64.
* Opraveno: tabulky $metrics nejsou zobrazeny pro účty Blob Storage
* Opraveno: uzel tabulky nefunguje pro místní úložiště (vývoj)

#### <a name="known-issues"></a>Známé problémy

* macOS instalace může vyžadovat zvýšenou úroveň oprávnění

06/15/2016
### <a name="version-080"></a>Verze 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nová

* Podpora sdílené složky souborů: zobrazování, ukládání, stahování, kopírování souborů a adresářů, identifikátorů URI SAS (vytvoření a připojení)
* Vylepšené uživatelské prostředí pro připojení k úložišti s klíči účtu nebo identifikátorů URI SAS
* Exportovat výsledky dotazů tabulky
* Změny pořadí sloupců tabulky a přizpůsobení
* Zobrazení kontejnerů objektů blob $logs a tabulky $metrics pro účty úložiště pomocí povolené metriky
* Vylepšené export a import chování, teď obsahuje typ hodnoty vlastnosti

#### <a name="fixes"></a>Opravy

* Opraveno: stahování velkých objektů BLOB nebo může způsobit neúplné nahrávání a stahování
* Opraveno: úpravy, přidáním nebo importováním entity s hodnotou číselný řetězec ("1") převede ho na double
* Opraveno: Nelze rozbalit uzel tabulky v místním vývojovém prostředí

#### <a name="known-issues"></a>Známé problémy

* tabulky $metrics nejsou viditelné pro účty Blob Storage
* Azure Queue programově přidat nemusí správně zobrazovat, pokud jsou zprávy zakódován pomocí kódování Base64

05/17/2016
### <a name="version-07201605090"></a>Verze 0.7.20160509.0

#### <a name="new"></a>Nová

* Lepší zpracování chyb pro aplikace, dojde k chybě

#### <a name="fixes"></a>Opravy

* Oprava chyby, kde informační panel zpráv někdy nezobrazují při přihlašovací údaje byly zapotřebí

#### <a name="known-issues"></a>Známé problémy

* Tabulky: Přidání, úpravy nebo import entity, která má vlastnost s nejednoznačnou číselnou hodnotou, jako je například "1" nebo "1,0" a uživatel se pokusí odeslat jako `Edm.String`, hodnota se vrátí prostřednictvím klientského rozhraní API jako EDM. Double

03/31/2016

### <a name="version-07201603250"></a>Verze 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nová

* Tabulka podpory: dotazování zobrazení, export, import a operace CRUD s entitami
* Fronty podpory: zobrazování, přidávání, dequeueing zpráv
* Při generování identifikátorů URI SAS pro účty úložiště
* Připojení k účtům úložiště pomocí identifikátorů URI SAS
* Oznámení o aktualizacích pro budoucí aktualizace do Průzkumníka služby Storage
* Aktualizovaný vzhled a chování

#### <a name="fixes"></a>Opravy

* Vylepšení výkonu a spolehlivosti

### <a name="known-issues-amp-mitigations"></a>Známé problémy &amp; zmírnění

* Stahování velkých objektů blob, souborů nefunguje správně – doporučujeme použít AzCopy, když jsme tento problém vyřešit
* Přihlašovací údaje k účtu nebudou načteny ani do mezipaměti, pokud domovskou složku se nenašel nebo se nedá zapisovat
* Pokud přidáváme, upravujeme nebo importujeme entitu, která má vlastnost s nejednoznačnou číselnou hodnotou, například "1" nebo "1,0", a uživatel se pokusí odeslat jako `Edm.String`, hodnota se vrátí pomocí klientského rozhraní API jako EDM. Double.
* Při importu souborů CSV se Víceřádkový záznamy, může získat data rozdělit nebo zamíchal

02/03/2016

### <a name="version-07201601291"></a>Verze 0.7.20160129.1

#### <a name="fixes"></a>Opravy

* Vylepšený celkový výkon při nahrávání, stahování a kopírování objektů BLOB

01/14/2016

### <a name="version-07201601050"></a>Verze 0.7.20160105.0

#### <a name="new"></a>Nová

* Podpora Linuxu (parity funkcí na OSX)
* Přidat kontejnery objektů blob pomocí klíče sdílených přístupových podpisů (SAS)
* Přidání účtů úložiště pro Azure Čína 21Vianet
* Přidání účtů úložiště s vlastní koncové body
* Otevřít a zobrazit obsah textu a obrázku objekty BLOB
* Zobrazit a upravit vlastnosti objektu blob a metadat

#### <a name="fixes"></a>Opravy

* Opraveno: nahrávání nebo stahování velkého počtu objektů BLOB (500 +) může někdy způsobit, že aplikace má bílé obrazovky
* Opraveno: Při nastavování úroveň veřejného přístupu pro kontejner objektů blob, nová hodnota není aktualizovány, dokud znovu nastavit fokus v kontejneru. Také dialogové okno vždy výchozí hodnota je "Bez veřejného přístupu" a ne skutečné aktuální hodnotu.
* Podpora lepší celkový/přístupnost přes klávesnici a uživatelského rozhraní
* Obtékání textu historie s popisem cesty po dlouhá prázdné znaky
* Dialogové okno SAS podporuje ověřování vstupu
* Místní úložiště i nadále k dispozici i v případě, že vypršela platnost přihlašovacích údajů uživatele
* Při odstranění kontejneru objektů blob v otevřeném v Průzkumníku objektů blob na pravé straně je uzavřen

#### <a name="known-issues"></a>Známé problémy

* Instalace pro Linux vyžaduje verzi gcc, aktualizován nebo upgradovat – postup upgradu jsou níže:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Verze 0.7.20151116.0

#### <a name="new"></a>Nová

* macOS a Windows verze
* Přihlášení k zobrazení vašich účtů úložiště – pomocí účtu organizace, Microsoft Account, 2FA atd.
* Místním vývojovým úložištěm (použití emulátoru úložiště jen pro Windows)
* Podpora Azure Resource Manager a Klasický model prostředků
* Vytvářet a odstraňovat objekty BLOB, fronty nebo tabulky
* Vyhledání konkrétních objektů BLOB, fronty nebo tabulky
* Seznamte se s obsahem kontejnery objektů blob
* Zobrazení a procházení adresářů
* Nahrání, stažení a odstranění objektů BLOB a složek
* Zobrazit a upravit vlastnosti objektu blob a metadat
* Generovat klíče SAS
* Správu a vytváření uložené zásady přístupu (SAP)
* Vyhledat bloby podle předpony
* Přetáhněte n soubory k odeslání nebo stažení

#### <a name="known-issues"></a>Známé problémy

* Při nastavování úroveň veřejného přístupu pro kontejner objektů blob, nová hodnota není aktualizován, dokud znovu nastavit fokus v kontejneru
* Když otevřete dialogové okno nastavit úroveň veřejného přístupu, vždy zobrazí "Bez veřejného přístupu" jako výchozí a ne skutečné aktuální hodnota
* Nelze přejmenovat stažené objekty BLOB
* Položky protokolu aktivit se někdy "zaseknou" v probíhající stavu, když dojde k chybě a chyba se nezobrazí.
* Někdy dojde k chybě nebo se změní na bílý zcela při pokusu o odeslání nebo stažení velkého počtu objektů BLOB
* Někdy zrušení operace kopírování nefunguje
* Při vytváření kontejneru (tabulka objektů blob nebo fronty /), pokud vstupní neplatný název a pokračovat a vytvořte další v rámci jiného kontejneru typu nelze nastavit fokus na nový typ
* Nelze vytvořit novou složku nebo přejmenovat složku




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
