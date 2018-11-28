---
title: Nasazení zařízení StorSimple řady 8000 na webu Azure Portal | Dokumentace Microsoftu
description: Popis kroků a osvědčených postupů pro nasazení zařízení StorSimple řady 8000 s aktualizací Update 3 nebo novější a služby Správce zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 34648426086bdd67f19ec227ddf0d931f3837c0d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237991"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Nasazení místního zařízení StorSimple (Update 3 a novější)

## <a name="overview"></a>Přehled
Vítejte v průvodci nasazením zařízení Microsoft Azure StorSimple. Tyto kurzy nasazení se týkají StorSimple řady 8000 s aktualizací Update 3 nebo novější. Tato série kurzů zahrnuje kontrolní seznam konfigurace, požadavky konfigurace a podrobné kroky konfigurace zařízení StorSimple.

Informace v těchto kurzech předpokládají, že jste si přečetli informace o bezpečnostních opatřeních a že jste zařízení StorSimple vybalili, namontovali do racku a zapojili jeho kabeláž. Pokud jste tyto kroky ještě neprovedli, začněte prostudováním [bezpečnostních opatření](storsimple-8000-safety.md). Podle pokynů ke konkrétnímu zařízení zařízení vybalte, namontujte do racku a zapojte jeho kabeláž.

* [Zařízení 8100 – Vybalení, namontování do racku a zapojení kabeláže](storsimple-8100-hardware-installation.md)
* [Zařízení 8600 – Vybalení, namontování do racku a zapojení kabeláže](storsimple-8600-hardware-installation.md)

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Doporučujeme, abyste si před zahájením práce prošli kontrolní seznam konfigurace. Proces nasazení a konfigurace může trvat nějakou dobu.

> [!NOTE]
> Informace o nasazení zařízení StorSimple publikované na webu Microsoft Azure platí pouze pro řadu zařízení StorSimple 8000. Kompletní informace o zařízeních řady 7000 najdete zde: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Informace o nasazení řady 7000 najdete v [Úvodní příručce k systému StorSimple](http://onlinehelp.storsimple.com/111_Appliance/). 


## <a name="deployment-steps"></a>Kroky nasazení
Pomocí níže uvedených požadovaných kroků zařízení StorSimple nakonfigurujte a připojte ke službě Správce zařízení StorSimple. Kromě požadovaných kroků existují ještě některé volitelné kroky a postupy, které může být vhodné při nasazení provést. Informace o tom, kdy je vhodné provést jednotlivé volitelné kroky, jsou vždy uvedeny v příslušných požadovaných krocích nasazení.

| Krok | Popis |
| --- | --- |
| **POŽADAVKY** |Tyto požadavky je nutné splnit v rámci přípravy na nasazení. |
| [Kontrolní seznam konfigurace nasazení](#deployment-configuration-checklist) |Kontrolní seznam použijte ke shromáždění a zaznamenání informací před nasazením a během nasazení. |
| [Požadavky nasazení](#deployment-prerequisites) |Splnění požadavků nasazení kvalifikuje prostředí jako připravené na nasazení. |
|  | |
| **PODROBNÝ POSTUP NASAZENÍ** |Tyto kroky jsou požadované k produkčnímu nasazení zařízení StorSimple. |
| [Krok 1: Vytvoření nové služby](#step-1-create-a-new-service) |Nastavte správu cloudu a úložiště pro zařízení StorSimple. *Pokud máte existující službu pro jiná zařízení StorSimple, tento krok přeskočte*. |
| [Krok 2: Získání registračního klíče služby](#step-2-get-the-service-registration-key) |Pomocí tohoto klíče zaregistrujte zařízení StorSimple ve službě správy a připojte je k ní. |
| [Krok 3: Konfigurace a registrace zařízení pomocí Windows PowerShellu pro StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Pokud chcete instalaci dokončit pomocí služby pro správu, připojte zařízení k síti a zaregistrujte ho v Azure. |
| [Krok 4: Dokončení minimální instalace zařízení](#step-4-complete-minimum-device-setup)</br>[Osvědčený postup: Aktualizace zařízení StorSimple](#scan-for-and-apply-updates) |Pomocí služby pro správu dokončete instalaci zařízení a aktivujte je k poskytování úložiště. |
| [Krok 5: Vytvoření kontejneru svazků](#step-5-create-a-volume-container) |Vytvořte kontejner ke zřízení svazků. Kontejner svazků obsahuje účet úložiště, šířku pásma a nastavení šifrování pro všechny svazky, které jsou v něm obsažené. |
| [Krok 6: Vytvoření svazku](#step-6-create-a-volume) |V zařízení StorSimple zřiďte svazky úložiště pro vaše servery. |
| [Krok 7: Připojení, inicializace a formátování svazků](#step-7-mount-initialize-and-format-a-volume)</br>[Volitelné: Konfigurace funkce MPIO](storsimple-8000-configure-mpio-windows-server.md) |Připojte své servery k úložišti iSCSI poskytovanému zařízením. Volitelně konfigurujte funkci MPIO, abyste zajistili, že servery budou tolerovat chyby připojení, sítě a rozhraní. |
| [Krok 8: Provedení zálohy](#step-8-take-a-backup) |Nastavte zásady zálohování pro ochranu dat. |
|  | |
| **DALŠÍ POSTUPY** |Tyto postupy můžete někdy potřebovat při nasazování svého řešení. |
| [Konfigurace nového účtu úložiště pro službu](#configure-a-new-storage-account-for-the-service) | |
| [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console) | |
| [Získání názvu IQN hostitele se systémem Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Vytvoření ruční zálohy](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Kontrolní seznam konfigurace nasazení
Před nasazením zařízení je potřeba shromáždit informace ke konfiguraci softwaru zařízení StorSimple. Předběžná příprava některých z těchto informací vám pomůže urychlit a zjednodušit proces nasazení zařízení StorSimple ve vašem prostředí. Stáhněte si tento kontrolní seznam a používejte ho k zaznamenávání podrobností konfigurace během nasazování zařízení.

* [Stáhnout kontrolní seznam konfigurace nasazení zařízení StorSimple](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Požadavky nasazení
Následující části popisují požadavky konfigurace pro službu Správce zařízení StorSimple a zařízení StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Máte účet Microsoft a přihlašovací údaje účtu.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Vaše předplatné Microsoft Azure je povoleno pro službu Správce zařízení StorSimple. Předplatné by mělo být zakoupeno prostřednictvím [smlouvy Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Máte přístup k softwaru pro emulaci terminálu, jako je například PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Zařízení v datovém centru
Před zahájením konfigurace zařízení se ujistěte, že je zařízení zcela vybaleno, namontováno v racku a je kompletně zapojena jeho kabeláž napájení, sítě a sériového přístupu, jak je popsáno v následujících článcích:

* [Zařízení 8100 – Vybalení, namontování do racku a zapojení kabeláže](storsimple-8100-hardware-installation.md)
* [Zařízení 8600 – Vybalení, namontování do racku a zapojení kabeláže](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru
Než začnete, ujistěte se, že:

* Porty brány firewall vašeho datového centra jsou otevřeny, aby umožňovaly přenos dat standardu iSCSI a cloudu, jak je popsáno v [Požadavcích sítě pro zařízení StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Podrobný postup nasazení
Pomocí následujících kroků nasaďte zařízení StorSimple v datovém centru.

## <a name="step-1-create-a-new-service"></a>Krok 1: Vytvoření nové služby
Služba Správce zařízení StorSimple může spravovat více zařízení StorSimple. Pomocí následujících kroků vytvořte instanci služby Správce zařízení StorSimple.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Pokud jste nepovolili automatické vytvoření účtu úložiště při vytvoření služby, po úspěšném vytvoření služby bude nutné vytvořit alespoň jeden účet úložiště. Tento účet úložiště se používá při vytváření kontejneru svazku.
>
> * Pokud jste nevytvořili účet úložiště automaticky, najdete podrobné pokyny k vytvoření účtu v tématu [Konfigurace nového účtu úložiště pro službu](#configure-a-new-storage-account-for-the-service).
> * Pokud jste automatické vytvoření účtu úložiště povolili, pokračujte na [krok 2: Získání registračního klíče služby](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Krok 2: Získání registračního klíče služby
Po vytvoření a spuštění služby Správce zařízení StorSimple je nutné získat registrační klíč služby. Tento klíč se používá k registraci a připojení zařízení StorSimple ke službě.

Na webu Azure Portal proveďte následující kroky.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Krok 3: Konfigurace a registrace zařízení pomocí Windows PowerShellu pro StorSimple
Pomocí Windows PowerShellu pro StorSimple dokončete počáteční nastavení zařízení StorSimple, jak je popsáno v následujícím postupu. K dokončení tohoto kroku je potřeba použít software pro emulaci terminálu. Další informace naleznete v tématu [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Krok 4: Dokončení minimální instalace zařízení
K provedení minimální konfigurace zařízení StorSimple je nutné provést následující kroky: 

* Zadejte popisný název zařízení.
* Nastavte časové pásmo zařízení.
* Oběma řadičům přiřadit pevné IP adresy.

K dokončení minimální instalace zařízení proveďte na webu Azure Portal následující kroky.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Po dokončení minimální instalace zařízení je osvědčeným postupem [vyhledání a instalace nejnovějších aktualizací](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Krok 5: Vytvoření kontejneru svazků
Kontejner svazků obsahuje účet úložiště, šířku pásma a nastavení šifrování pro všechny svazky, které jsou v něm obsažené. Kontejner svazků je nutné vytvořit před zahájením zřizování svazků v zařízení StorSimple.

Provedením následujících kroků na webu Azure Portal vytvořte kontejner svazků.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Krok 6: Vytvoření svazku
Po vytvoření kontejneru svazků můžete v zařízení StorSimple zřídit svazek úložiště pro své servery. Provedením následujících kroků na webu Azure Portal vytvořte svazek.

> [!IMPORTANT]
> Služba Správce zařízení StorSimple umožňuje vytvářet dynamicky i zcela zřizované svazky. Nelze však vytvářet částečně zřizované svazky.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Krok 7: Připojení, inicializace a formátování svazků
Následující kroky se provádějí v hostiteli se systémem Windows Server.

> [!IMPORTANT]
> * K zajištění vysoké dostupnosti vašeho řešení StorSimple doporučujeme před konfigurací standardu iSCSI nakonfigurovat na hostitelských serverech funkci MPIO (volitelné). Konfigurace funkce MPIO na hostitelských serverech zajistí, že servery budou tolerovat chyby připojení, sítě a rozhraní.
> * Pokyny k instalaci a konfiguraci funkce MPIO a standardu iSCSI v hostiteli se systémem Windows Server najdete v tématu [Konfigurace funkce MPIO pro zařízení StorSimple](storsimple-8000-configure-mpio-windows-server.md). Tyto kroky zahrnují také kroky připojení, inicializace a formátování svazků StorSimple.
> * Pokyny k instalaci a konfiguraci funkce MPIO a standardu iSCSI v hostiteli se systémem Linux najdete v tématu [Konfigurace funkce MPIO pro hostitele zařízení StorSimple se systémem Linux](storsimple-configure-mpio-on-linux.md)

Pokud se rozhodnete, že funkci MPIO konfigurovat nebudete, připojte, inicializujte a formátujte svazky zařízení StorSimple v hostiteli se systémem Windows Server pomocí následujících kroků.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Krok 8: Provedení zálohy
Zálohy vytvořené v určitých časových bodech poskytují ochranu a zvyšují možnost jejich obnovení při současném zkrácení doby potřebné k obnovení. Zařízení StorSimple lze zálohovat dvěma různými způsoby: pomocí místních snímků nebo pomocí cloudových snímků. Každý z těchto typů zálohování může být **naplánovaný** nebo **ruční**.

Provedením následujících kroků na webu Azure Portal vytvořte naplánované zálohování.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Ruční zálohování lze provést kdykoliv. Popis potřebného postupu najdete v tématu [Vytvoření ruční zálohy](#create-a-manual-backup).

Dokončili jste konfiguraci zařízení.

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurace nového účtu úložiště pro službu
Toto je volitelný krok, který je potřeba provést pouze v případě, že jste ve své službě nepovolili automatické vytvoření účtu úložiště. K vytvoření kontejneru svazků zařízení StorSimple je potřeba účet služby Microsoft Azure Storage.

Pokud potřebujete vytvořit účet služby Azure Storage v jiné oblasti, postupujte podle pokynů v tématu [Účty Azure Storage](../storage/common/storage-create-storage-account.md).

Na webu Azure Portal na stránce **služby Správce zařízení StorSimple** proveďte následující kroky.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Použití klienta PuTTY k připojení ke konzole sériového portu zařízení
K připojení Windows PowerShellu pro StorSimple je nutné použít software pro emulaci terminálu, jako je například PuTTY. PuTTY můžete použít při přístupu k zařízení přímo pomocí konzoly sériového portu nebo otevřením relace Telnetu ze vzdáleného počítače.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Vyhledání a instalace aktualizací
Aktualizace zařízení může trvat několik hodin. Podrobné pokyny k instalaci nejnovější aktualizace najdete v tématu s popisem [instalace aktualizace Update 5](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Získání názvu IQN hostitele se systémem Windows Server
Pomocí následujících kroků získejte název IQN (iSCSI Qualified Name) hostitele Windows se systémem Windows Server® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Vytvoření ruční zálohy
Provedením následujících kroků na webu Azure Portal vytvořte ruční zálohu jednoho svazku zařízení StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>Zapojení kontaktů sériového kabelu pro StorSimple
Následující zapojení kontaktů lze použít pro sériový kabel konzole StorSimple.

Konektor DB9 se zdířkami je označený jako P1 a 3,5mm konektor jako P2.

![Zapojení kontaktů 1 pro sériový kabel konzole StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

Špička stereo konektoru se považuje za PIN 3 RX, střed za PIN 2 TX a základna za PIN 1 GND, jak znázorňuje následující obrázek.

![Zapojení kontaktů 2 pro sériový kabel konzole StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Další kroky
* [Konfigurace řešení StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md)
* [Použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md)

