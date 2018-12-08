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
ms.openlocfilehash: 956482a30d383df558eee775b9d89c211bc53e61
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101411"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Zpráva k vydání verze Microsoft Azure Storage Explorer

Tento článek obsahuje poznámky k verzi Azure Storage Explorer 1.4.3, stejně jako zpráva k vydání verze pro předchozí verze.

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Storage ve Windows, macOS a Linuxu.

## <a name="version-150"></a>Verze 1.5.0
29 10. 2018

### <a name="download-azure-storage-explorer-150"></a>Stáhněte si Průzkumníka služby Azure Storage 1.5.0
- [Průzkumník služby Azure Storage 1.5.0 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Průzkumník služby Azure Storage 1.5.0 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Průzkumník služby Azure Storage 1.5.0 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Teď můžete použít [AzCopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) pro nahrávání a stahování objektů BLOB. Chcete-li povolit tuto funkci přejděte do nabídky "Experimentální" a klikněte na "Použití AzCopy pro vylepšené objektů Blob nahrávání a stahování". Když je povoleno, AzCopy se použijí v následujících scénářích:
   * Nahrání složky a soubory do kontejnerů objektů blob, buď pomocí panelu nástrojů nebo přetažení.
   * Stahování složek a souborů, buď pomocí panelu nástrojů nebo místní nabídky.

* Kromě toho při použití nástroje AzCopy:
   * Můžete zkopírovat příkaz AzCopy používaný k provedení převodu do schránky. Jednoduše klikněte na tlačítko "Kopírovat AzCopy příkaz do schránky" v protokolu aktivit.
   * Je potřeba ručně aktualizovat editor objektů blob po nahrání.
   * Nahrávání souborů doplňovací objekty BLOB se nepodporuje, se nahraje .vhds jako objekty BLOB stránky a nahraje všechny ostatní soubory jako objekty BLOB bloku.
   * Chyby a ke kterým mohlo dojít během nahrávání nebo stahování se zobrazí až po nahrání nebo stažení je dokončené.

Podpora pro použití nástroje AzCopy s využitím sdílených složek a konečně, bude dostupná v budoucnu.
* Průzkumník služby Storage je teď používá elektronovým verze 2.0.11.
* Přerušení zapůjčení můžete nyní provést pouze na jeden objekt blob najednou. Kromě toho je nutné zadat název objektu blob zapůjčení, jejichž jsou zásadní. Tato změna byla provedena snížit pravděpodobnost, že omylem přerušení zapůjčení, zejména v případě .vhds pro virtuální počítače. #394
* Pokud někdy narazíte na problémy s přihlašováním, můžete teď zkuste resetovat ověřování. Přejděte do nabídky "Nápověda" a klikněte na tlačítko "Obnovit" přístupu k této funkci. #419

### <a name="fix"></a>Napravit

* Po zpětné vazby uživatelů silné výchozí emulátor uzel se znovu zapnout. Můžete přesto přidat další emulátor připojení přes dialogové okno připojit, ale pokud vaše emulátor je nakonfigurován pro použití výchozí porty můžete použít také uzel "Emulátor * výchozí porty" v části "Místní a připojené a ukládání účtů". #669
* Průzkumník služby Storage už vám umožní nastavit hodnoty metadat objektu blob, které mají počáteční ani koncové prázdné znaky. #760
* Tlačítko "Sign In" se vždy povolena na stejné stránkách dialogového okna připojit. V případě potřeby je nyní zakázán. #761
* Rychlý přístup už vygeneruje chybu v konzole když byly přidány žádné položky rychlý přístup.

### <a name="known-issues"></a>Známé problémy

* Odpojení od zdroje připojené pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání jiné přílohy z zobrazovat správně. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit 537 # pro další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to tak není překážku, zadejte komentář k tomuto problému.
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým. Chcete-li vyřešit tento problém můžete při odesílání nebo stahování z kontejneru objektů blob, můžete použít experimentální funkce AzCopy.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce. Pokus o použití těchto funkcí při práci s Azure Stackem zdrojů může vést k neočekávaným chybám.
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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

## <a name="previous-releases"></a>Předchozí verze

* [Verze 1.4.4](#version-144)
* [Verze 1.4.3](#version-143)
* [Verze 1.4.2](#version-142)
* [Verze 1.4.1](#version-141)
* [Verze 1.3.0](#version-130)
* [Verzi 1.2.0](#version-120)
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

## <a name="version-144"></a>Verze 1.4.4
10/15/2018

### <a name="hotfixes"></a>Opravy hotfix
* Verze rozhraní Api Azure Resource Management byla vrácena zpět k odblokování uživatelů Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
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
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Verze rozhraní Api Azure Resource Management byla vrácena zpět k odblokování uživatelů Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
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
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Aktualizujte verzi rozhraní Api správy prostředků Azure na 2018-07-01 a přidat podporu pro nové typy účtu úložiště Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

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
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Koncového bodu autority pro přihlášení k Azure US Government se změnil na https://login.microsoftonline.us/
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
* Odpojení od zdroje připojené pomocí identifikátoru URI SAS, jako je například kontejner objektů blob, může způsobit chybu, která brání jiné přílohy z zobrazovat správně. Chcete-li tento problém obejít, jednoduše obnovte uzel skupiny. Zobrazit [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/537) Další informace.
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* Azure Stack nepodporuje následující funkce a pokus o použití při práci s Azure Stack může vést k neočekávaným chybám:
   * Sdílené složky
   * Úrovně přístupu
   * Obnovitelné odstranění
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Obnovení snímku se přidala do kontextové nabídky editoru sdílené složky souborů při prohlížení snímek sdílené složky. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Nyní je vždy povolena na tlačítko Vymazat frontu. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
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
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Průzkumník služby Storage teď podporuje úrovně přístupu pro pouze objektů Blob a účty úložiště GPV2. Další informace o úrovních přístupu [tady](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
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
* Přesun fokusu tabulátorem nebyl viditelný pro tlačítka v dialogových oknech při použití motivu černé hybridní připojení. Nyní je vidět. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Použití malých a velkých "Automaticky vyřešit" pro akce v protokolu aktivit byla chybná. Nyní je správná. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Při odstraňování entity z tabulky, zobrazí se dialogové okno s výzvou k potvrzení ikona chyby. Dialogové okno nyní používá ikonou upozornění. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Známé problémy
* Pokud používáte VS pro Mac a již někdy vytvářeli vlastní konfiguraci AAD, je možná nebudete moct přihlásit. Chcete-li tento problém obejít, odstraňte její obsah ~ /. IdentityService/AadConfigurations. Pokud to není překážku, zadejte komentář na [tento problém](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nebyla ještě implementována plně všechna rozhraní API úložiště. Z toho důvodu může být neočekávané chyby nebo chování při použití Azurite k vývojovým úložištěm.
* Ve výjimečných případech můžou uváznout stromu fokus na rychlý přístup. Chcete-li unstick fokus, můžete aktualizovat vše.
* Nahrává se ze složky Onedrivu nefunguje z důvodu chyby v NodeJS. Chyb bylo opraveno, ale zatím nejsou integrované do elektronovým.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Storage Explorer zpětné vazby se nyní nachází na Githubu. Naši stránku problémy dostat kliknutím na tlačítko zpětná vazba v dolní části vlevo nebo tak, že přejdete do [ https://github.com/Microsoft/AzureStorageExplorer/issues ](https://github.com/Microsoft/AzureStorageExplorer/issues). Nebojte se posílat návrhy na vylepšení, hlášení problémů, klást otázky nebo ponechte jakoukoli jinou formu zpětnou vazbu.
* Pokud dochází k problémům certifikát SSL a nepovedlo se najít problematický certifikát, teď můžete spustit Průzkumníka služby Storage z příkazového řádku pomocí `--ignore-certificate-errors` příznak. Při spuštění s tímto příznakem, Průzkumníka služby Storage se bude ignorovat chyby certifikátů SSL.
* Teď je k dispozici možnost "Stáhnout" v místní nabídce pro objekt blob a souboru položky.
* Lepší dostupnost a podpora čtečky obrazovky. Pokud se spoléháte na funkce pro usnadnění přístupu, přečtěte si naše [dokumentace k usnadnění](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) Další informace.
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
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Pro uživatele systému Linux, budete muset nainstalovat [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
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
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtr Storno [tady](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

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
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

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
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

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
* Oznámení o aktualizaci pro některé uživatele ve 0.9.0 poškozený. Opravili jsme problém, a pro ty ovlivněny chyby, můžete ručně stáhnout nejnovější verzi Storage Exploreru [tady](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Průzkumník" a "Správa účtů zobrazení" by měl být Ctrl / Cmd + Shift + E a Ctrl / Cmd + Shift + A v uvedeném pořadí.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

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
* Oznámení o aktualizaci pro některé uživatele ve 0.9.0 poškozený. Opravili jsme problém, a pro ty ovlivněny chyby, můžete ručně stáhnout nejnovější verzi Storage Exploreru [zde](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Známé problémy
* Průzkumník služby Storage nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Průzkumník" a "Správa účtů zobrazení" by měl být Ctrl / Cmd + Shift + E a Ctrl / Cmd + Shift + A v uvedeném pořadí.
* Při cílení na služby Azure Stack, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko "Storno" na úkol, může trvat nějakou dobu tuto úlohu zrušit. Je to proto, že se používá zde popsané řešení filtr Storno.
* Pokud se rozhodnete nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby měla zapomenout rozhodnutí Průzkumníka služby Storage.
* Panel nastavení účtu může zobrazit, že budete muset znovu zadat přihlašovací údaje k filtrování předplatných.
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování.
* I když Azure Stack v současné době nepodporuje sdílené složky, se zobrazí stále uzlu sdílených složek v účtu služby storage připojené služby Azure Stack.
* Elektronovým prostředí používané Průzkumníka služby Storage má potíže s hardwarovou akceleraci některé GPU (grafický procesor). Pokud Průzkumník služby Storage se zobrazuje hlavní okno prázdné (prázdné), můžete zkusit spustíte Průzkumníka služby Storage z příkazového řádku a zakázání GPU akceleraci tak, že přidáte `--disable-gpu` přepínače:

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
* Řešení potíží s Průzkumníka služby Storage [Průvodce][2]
* [Pokyny] [ 3] o připojení k předplatnému Azure Stack

### <a name="known-issues"></a>Známé problémy

* Tlačítka na potvrzovací dialogové okno Odstranit složku nezaregistrujete s myší v Linuxu. Alternativním řešením je použití klávesy Enter
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

* Řešení potíží s Průzkumníka služby Storage [Průvodce][2]
* [Pokyny] [ 3] o připojení k předplatnému Azure Stack

#### <a name="fixes"></a>Opravy

* Opraveno: Nahrání souboru využili vysokou příležitost sestavení z důvodu nedostatku paměti
* Opraveno: Můžete teď přihlášení pomocí PIN kódu nebo čipová karta
* Opraveno: Otevřete portálu teď funguje s Azure China, Azure Germany, Azure US Government a Azure Stack
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
* Teď můžete přidat důvěryhodný Base-64 kódovaný certifikáty X.509 SSL tak, že přejdete k úpravě -&gt; certifikáty SSL –&gt; importovat certifikáty

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
* Můžete teď přihlášení k účtům Azure China, Azure Germany a Azure US Government.
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
* Teď můžete otevřít více editory na různých záložkách. Jediným kliknutím zobrazíte dočasné kartu; Otevřít poklikáním na kartě trvalé. Můžete také kliknout na kartě dočasné k němu trvalé kartu
* Vylepšili jsme zřetelný rozdíl ve výkonu a vylepšení stability pro odesílání a stahování souborů, zejména u velkých souborů na rychlé počítače
* Prázdné složky "virtuální" teď je možné vytvářet v kontejnerech objektů blob
* Znovu zavedli jsme cíleně prohledejte s naší nové hledání dílčího řetězce rozšířené, takže Teď máte dvě možnosti pro hledání:
    * Globální vyhledávání – stačí do textového pole hledání zadejte hledaný termín
    * Cíleně prohledejte – klikněte na ikonu lupy vedle uzlu, pak přidat hledaný termín do konce cesty, nebo klikněte pravým tlačítkem a vyberte "Vyhledávání z tady"
* Přidali jsme různé motivy: světla (výchozí), tmavý, vysoký kontrast – černá a Vysoký kontrast – bílá. Přejděte na Úpravy -&gt; motivy, chcete-li změnit vaši volbu motivů
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
* Přejmenování objektů BLOB (jednotlivě nebo uvnitř kontejneru objektů blob přejmenováno) se nezachová snímky. Všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity jsou zachovány při přejmenování
* Kopírování nebo přejmenování zdroje nefunguje v rámci účty připojené sdíleným přístupovým Podpisem

07/07/2016
### <a name="version-082"></a>Verze 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nová

* Účty úložiště jsou seskupené podle předplatných; vývojovým úložištěm a prostředkům připojeným prostřednictvím klíč nebo SAS se zobrazí v uzlu (místní a připojené)
* Odhlaste se z účtů v panelech "Nastavení účtu Azure"
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

* Tabulky: Přidání, úprava nebo import entita, která má vlastnost ambiguously číselnou hodnotu, jako je například "1" nebo "1.0" a uživatel se pokusí odeslat ji jako `Edm.String`, hodnota bude vracet přes rozhraní API jako Edm.Double klienta

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

### <a name="known-issues-amp-mitigations"></a>Známé problémy &amp; způsoby zmírnění rizik

* Stahování velkých objektů blob, souborů nefunguje správně – doporučujeme použít AzCopy, když jsme tento problém vyřešit
* Přihlašovací údaje k účtu nebudou načteny ani do mezipaměti, pokud domovskou složku se nenašel nebo se nedá zapisovat
* Pokud jsme jsou přidání, úprava nebo import entita, která má vlastnost ambiguously číselnou hodnotu, jako je například "1" nebo "1.0", a uživatel se pokusí odeslat ji jako `Edm.String`, hodnota bude vracet přes rozhraní API jako Edm.Double klienta
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
* Přidání účtů úložiště pro Azure Čína
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
