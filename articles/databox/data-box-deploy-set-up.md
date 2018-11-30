---
title: Nastavení Azure Data Boxu | Microsoft Docs
description: Postup zapojení kabeláže a připojení k Azure Data Boxu
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: alkohli
ms.openlocfilehash: 78e56286e0dec3307c7481a6097c53a1362f57cc
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427518"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Kurz: Zapojení kabeláže a připojení k Azure Data Boxu

Tento kurz popisuje, jak zapojit kabeláž, připojit a zapnout Azure Data Box.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu
> * Připojení k Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Objednání Azure Data Boxu](data-box-deploy-ordered.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**. 
    - V průhledném pouzdře připevněném k zařízení pod elektrickým kabelem najdete expediční štítek. Tento štítek bezpečně uschovejte, protože ho budete potřebovat pro zpáteční zásilku.
    - Některé oblasti v Evropě se může zobrazit zařízení zabalené v poli. Ujistěte se, že máte rozbalit zařízení a uložit do pole pro zpáteční zásilku.
3. Prostudovali jste si [bezpečnostní pokyny k Data Boxu](data-box-safety.md).
4. Obdrželi jste jeden uzemněný napájecí kabel pro použití s 100TB úložným zařízením.
5. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte připojení 10 GbE, je možné použít datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování. 
6. Data Box je potřeba umístit na rovnou plochu. Pokud chcete zařízení umístit na standardní polici v racku, potřebujete v racku datového centra slot 7U. Zařízení můžete v racku umístit na plocho nebo na výšku.
7. Pro připojení Data Boxu k hostitelském počítači je potřeba pořídit následující kabely.
    - Dva měděné kabely 10 GbE SFP+ Twinax (pro síťová rozhraní DATA 1, DATA 2)
    - Jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní MGMT)
    - Jeden síťový kabel RJ-45 kategorie 6A NEBO jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní DATA 3 konfigurované jako 10 Gb/s a 1 Gb/s v tomto pořadí)

## <a name="cable-your-device"></a>Zapojení kabeláže zařízení

Při zapojování kabeláže zařízení postupujte následovně.

1. Zkontrolujte zařízení, zda nenese stopy porušení nebo není zjevně poškozeno. Pokud je zařízení porušené nebo vážně poškozené, nepokračujte. Okamžitě se obraťte na podporu Microsoftu, aby vám pomohla určit, jestli je zařízení v dobrém funkčním stavu nebo zda je potřeba poslat náhradní.
2. Přeneste zařízení do umístění, ve kterém ho chcete zapnout. Položte zařízení na rovnou plochu. Zařízení můžete umístit také na standardní polici v racku.
3. Zapojte napájecí a síťový kabel. Propojovací rozhraní připojeného zařízení pro běžnou konfiguraci je znázorněné níže. 
    
    ![Zapojená kabeláž propojovacího rozhraní zařízení Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Zapojte napájecí kabel do označeného umístění síťového napájení. Druhý konec napájecího kabelu by měl být připojený k rozvodné energetické jednotce.
    2. Pomocí kabelu RJ-45 kategorie 6 propojte port MGMT na jednom konci a přenosný počítač na druhém konci.            
    3. Kabel RJ-45 kategorie 6A zapojte na jedné straně k portu DATA 3. Pokud k síťovému rozhraní DATA 3 připojíte kabel RJ-45 kategorie 6A, nakonfiguruje se jako 10 GbE. Pokud k němu připojíte kabel RJ-45 kategorie 6, nakonfiguruje se jako 1 GbE.
    4. Pomocí měděných kabelů 10 GbE SFP+ Twinax propojte porty DATA 1 a DATA 2 v tomto pořadí. 
    5. Druhé konce kabelů z datových portů jsou připojené k hostitelskému počítači přes přepínač 10 GbE.

4. Na předním ovládacím panelu zařízení vyhledejte tlačítko napájení. Zapněte zařízení.

    ![Tlačítko napájení Data Boxu](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Připojení k zařízení

Pomocí následujícího postupu nastavte své zařízení s využitím místního webového uživatelského rozhraní a uživatelského rozhraní portálu.

1. Na adaptéru sítě Ethernet na přenosném počítači, pomocí kterého se připojujete k zařízení, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. 
2. Připojte se k portu MGMT vašeho zařízení a přejděte na jeho místní webové uživatelské rozhraní na adrese https://192.168.100.10. Po zapnutí zařízení to může trvat až 5 minut.
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

Po dokončení nastavení zařízení se můžete připojit ke sdíleným složkám zařízení a zkopírovat do zařízení data z vašeho počítače. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu
> * Připojení k Data Boxu

V dalším kurzu se dozvíte, jak do Data Boxu zkopírovat data.

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu](./data-box-deploy-copy-data.md)

