---
title: Kurz nastavení Azure Data Boxu | Microsoft Docs
description: V tomto kurzu se naučíte, jak zapojit kabeláž Azure Data Boxu, připojit Azure Data Box a zapnout Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ac87d5040cd572635d81be51308f48a57ddd38e3
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335464"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Kurz: Zapojení kabeláže a připojení Azure Data Boxu

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Zapojení kabeláže a připojení k zařízení

::: zone-end

::: zone target="docs"

Tento kurz popisuje, jak zapojit kabeláž, připojit a zapnout Azure Data Box.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu
> * Připojení k Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Zadali jste objednávku Azure Data Boxu.
    - V případě objednávky importu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-ordered.md).
    - V případě objednávky exportu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-export-ordered.md).
1. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**. 
    - V průhledném pouzdře připevněném k zařízení pod elektrickým kabelem najdete expediční štítek. Tento štítek bezpečně uschovejte, protože ho budete potřebovat pro zpáteční zásilku.
    - V některých oblastech Evropy může zařízení přijít zabalené v krabici. Nezapomeňte zařízení rozbalit a krabici si uschovejte pro zpáteční zásilku.
1. Prostudovali jste si [bezpečnostní pokyny k Data Boxu](data-box-safety.md).
1. Obdrželi jste jeden uzemněný napájecí kabel pro použití s 100TB úložným zařízením.
1. Máte hostitelský počítač, který se používá ke kopírování dat do (objednávka importu) nebo z (objednávka exportu) Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte připojení 10 GbE, je možné použít datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování. 
1. Data Box je potřeba umístit na rovnou plochu. Pokud chcete zařízení umístit na standardní polici v racku, potřebujete v racku datového centra slot 7U. Zařízení můžete v racku umístit na plocho nebo na výšku.
1. Pro připojení Data Boxu k hostitelském počítači je potřeba pořídit následující kabely.
    - Alespoň jeden měděný kabel 10 GbE SFP+ Twinax nebo optický kabel SFP+ (pro síťová rozhraní DATA 1, DATA 2). Data Box obsahuje síťové rozhraní Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T Adapters w/ PCI Express 3.0, takže kabely kompatibilní s tímto rozhraním by měly fungovat. Například při interním testování jsme použili kabel CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M. Další informace najdete v [seznamu podporovaných kabelů a přepínačů od společnosti Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní MGMT)
    - Jeden síťový kabel RJ-45 kategorie 6A NEBO jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní DATA 3 konfigurované jako 10 Gb/s a 1 Gb/s v tomto pořadí)

## <a name="cable-your-device"></a>Zapojení kabeláže zařízení

Při zapojování kabeláže zařízení postupujte následovně.

1. Zkontrolujte zařízení, zda nenese stopy porušení nebo není zjevně poškozeno. Pokud je zařízení porušené nebo vážně poškozené, nepokračujte. Okamžitě se obraťte na podporu Microsoftu, aby vám pomohla určit, jestli je zařízení v dobrém funkčním stavu nebo zda je potřeba poslat náhradní.
2. Přeneste zařízení do umístění, ve kterém ho chcete zapnout. Položte zařízení na rovnou plochu. Zařízení můžete umístit také na standardní polici v racku.
3. Zapojte napájecí a síťový kabel. Propojovací rozhraní připojeného zařízení pro běžnou konfiguraci je znázorněné níže. V závislosti na vašem prostředí můžete zvolit i jiné [možnosti zapojení kabeláže](data-box-cable-options.md).
    
    ![Zapojená kabeláž propojovacího rozhraní zařízení Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Zapojte napájecí kabel do označeného umístění síťového napájení. Druhý konec napájecího kabelu by měl být připojený k rozvodné energetické jednotce.
    2. Pomocí kabelu RJ-45 kategorie 6 propojte port MGMT na jednom konci a přenosný počítač na druhém konci.            
    3. Kabel RJ-45 kategorie 6A zapojte na jedné straně k portu DATA 3. Pokud k síťovému rozhraní DATA 3 připojíte kabel RJ-45 kategorie 6A, nakonfiguruje se jako 10 GbE. Pokud k němu připojíte kabel RJ-45 kategorie 6, nakonfiguruje se jako 1 GbE.
    4. V závislosti na síťových rozhraních, která chcete připojit za účelem přenosu dat, použijte k připojení portů DATA1 a DATA2 až dva měděné kabely 10-GbE SFP+ Twinax nebo optické kabely SFP+. 
    5. Druhé konce kabelů z datových portů jsou připojené k hostitelskému počítači přes přepínač 10 GbE.

4. Na předním ovládacím panelu zařízení vyhledejte tlačítko napájení. Zapněte zařízení.

    ![Tlačítko napájení Data Boxu](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Jakmile zařízení obdržíte, je potřeba zapojit jeho kabeláž a připojit se k němu. 

## <a name="cable-your-device"></a>Zapojení kabeláže zařízení

1. Pokud vidíte, že je zařízení poškozené nebo že s ním někdo manipuloval, nepokračujte. Požádejte podporu Microsoftu o dodání náhradního zařízení.
2. Před zapojením zařízení zkontrolujte, že máte následující kabely:
    
    - Uzemněný napájecí kabel 10 A nebo více s konektorem IEC60320 C-13 na jednom konci pro připojení k zařízení.
    - Jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní MGMT)
    - Dva měděné kabely 10 GbE SFP+ Twinax (pro síťová rozhraní DATA 1, DATA 2 10 Gb/s)
    - Jeden síťový kabel RJ-45 kategorie 6A NEBO jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní DATA 3 konfigurované jako 10 Gb/s a 1 Gb/s v tomto pořadí)

3. Zařízení vyjměte a položte na rovnou plochu. 
    
4. K zařízení připojte kabely, jak vidíte níže.  

    ![Zapojená kabeláž propojovacího rozhraní zařízení Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. K zařízení připojte napájecí kabel.
    2. Pomocí síťového kabelu RJ-45 kategorie 6 připojte hostitelský počítač k portu pro správu (MGMT) na zařízení. 
    3. Pomocí měděného kabelu SFP+ Twinax připojte alespoň jedno síťové rozhraní s rychlostí 10 Gb/s (tomu se dává přednost před rozhraním s rychlostí 1 Gb/s), DATA 1 nebo DATA 2 pro data. 
    4. Zapněte zařízení. Tlačítko napájení je na předním panelu zařízení.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Připojení k zařízení

Pomocí následujícího postupu nastavte své zařízení s využitím místního webového uživatelského rozhraní a uživatelského rozhraní portálu.

1. Na adaptéru sítě Ethernet na přenosném počítači, pomocí kterého se připojujete k zařízení, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. 
2. Připojte se k portu MGMT vašeho zařízení a přejděte na jeho místní webové uživatelské rozhraní na adrese https\://192.168.100.10. Po zapnutí zařízení to může trvat až 5 minut.
3. Klikněte na **Podrobnosti** a pak na **Pokračovat na web**.

   ![Připojení k místnímu webovému uživatelskému rozhraní](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png)

4. Zobrazí se **přihlašovací** stránka místního webového uživatelského rozhraní. Ujistěte se, že je sériové číslo zařízení stejné v uživatelském rozhraní portálu i v místním webovém uživatelském rozhraní. V tuto chvíli je zařízení uzamčené.
5. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
6. Stáhněte si z portálu přihlašovací údaje zařízení. Přejděte na **Obecné > Podrobnosti zařízení**. Zkopírujte si **Heslo zařízení**. Heslo zařízení se na portálu váže ke konkrétní objednávce. 

    ![Získání přihlašovacích údajů zařízení](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Zadejte heslo zařízení, které jste v předchozím kroku získali z webu Azure Portal, a přihlaste se k místnímu webovému uživatelskému rozhraní zařízení. Klikněte na **Přihlásit se**.
8. Na **řídicím panelu** se ujistěte, že jsou nakonfigurovaná síťová rozhraní. 
   - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. 
   - Pokud protokol DHCP není povolený, přejděte na **Nastavení síťových rozhraní** a v případě potřeby přiřaďte statické IP adresy.

     ![Řídicí panel zařízení](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Jakmile budou nakonfigurovaná datová síťová rozhraní, můžete k získání přístupu k místnímu webovému uživatelskému rozhraní na adrese `https://<IP address of a data network interface>` použít také IP adresu libovolného rozhraní DATA 1 až DATA 3. 

Po dokončení nastavení zařízení se můžete připojit ke sdíleným složkám zařízení a zkopírovat do zařízení data. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Připojení zařízení

1. Pokud chcete získat heslo zařízení, přejděte na webu [Azure Portal](https://portal.azure.com) na **Obecné > Podrobnosti o zařízení**.
2. Na adaptéru sítě Ethernet na počítači, pomocí kterého se připojujete k Data Boxu, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. Přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`. Připojení zařízení po zapnutí může trvat až 5 minut. 
3. Přihlaste se pomocí hesla z webu Azure Portal. Zobrazí se vám chyba s informacemi k potížím s certifikátem zabezpečení webu. Postupujte podle pokynů pro konkrétní prohlížeč a přejděte tak na webovou stránku.
4. Standardně je pro nastavení sítě pro datové rozhraní 10 Gb/s (nebo 1 Gb/s) nakonfigurovaný protokol DHCP. V případě potřeby můžete toto rozhraní nakonfigurovat jako statické a zadat IP adresu. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu
> * Připojení k Data Boxu

V dalším kurzu se dozvíte, jak zkopírovat data.

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu pro objednávku importu](./data-box-deploy-copy-data.md)

Nebo

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu pro objednávku exportu](./data-box-deploy-export-copy-data.md)

::: zone-end

