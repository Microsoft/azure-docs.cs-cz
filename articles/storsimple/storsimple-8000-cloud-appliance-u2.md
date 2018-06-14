---
title: StorSimple Cloud Appliance s aktualizací Update 3| Dokumentace Microsoftu
description: Naučte se vytvářet, nasazovat a spravovat řešení StorSimple Cloud Appliance ve virtuální síti Microsoft Azure. (Platí pro StorSimple s aktualizací Update 3 a novější).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: 4d47b5426da5d857085991767faa5fb227476408
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852332"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Nasazení a správa řešení StorSimple Cloud Appliance v Azure (s aktualizací Update 3 a novější)

## <a name="overview"></a>Přehled

Řešení StorSimple Cloud Appliance řady 8000 Series je další schopností dodávanou s řešením Microsoft Azure StorSimple. Řešení StorSimple Cloud Appliance běží na virtuálním počítači ve virtuální síti Microsoft Azure a slouží k zálohování a klonování dat z hostitelů.

Tento článek popisuje podrobný postup nasazení a správy řešení StorSimple Cloud Appliance v Azure. Po přečtení tohoto článku:

* Budete chápat, jak se liší cloudové zařízení od fyzického zařízení.
* Budete schopni vytvořit a nakonfigurovat cloudové zařízení.
* Budete schopni se připojit ke cloudovému zařízení.
* Zjistíte, jak pracovat s cloudovým zařízením.

Tento kurz se vztahuje na všechna řešení StorSimple Cloud Appliance s aktualizací Update 3 a novější.

#### <a name="cloud-appliance-model-comparison"></a>Porovnání modelů cloudových zařízení

Řešení StorSimple Cloud Appliance je k dispozici ve dvou modelech – standardní 8010 (dříve označovaný jako 1100) a prémiový 8020 (zaveden v aktualizaci Update 2). Následující tabulka obsahuje porovnání obou modelů.

| Model zařízení | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximální kapacita** |30 TB |64 TB |
| **Virtuální počítač Azure** |Standard_A3 (4 jádra, 7 GB paměti)| Standard_DS3 (4 jádra, 14 GB paměti)|
| **Dostupnost v oblastech** |Všechny oblasti Azure |Oblasti Azure, které podporují službu Storage úrovně Premium a virtuální počítače Azure DS3<br></br>Použijte [tento seznam](https://azure.microsoft.com/regions/services/), abyste zjistili, jestli jsou ve vaší oblasti dostupné obě možnosti **Virtuální počítače > DS-series** a **Úložiště > Diskové úložiště**. |
| **Typ úložiště** |Pro místní disky používá službu Azure Standard Storage<br></br> Zjistěte, jak [vytvořit účet služby Standard Storage](../storage/common/storage-create-storage-account.md) |Pro místní disky používá Azure Premium Storage.<sup>2</sup> <br></br>Zjistěte, jak [vytvořit účet služby Premium Storage](../virtual-machines/windows/premium-storage.md) |
| **Pokyny týkající se úloh** |Načítání souborů ze zálohy na úrovni položek |Scénáře vývoje a testování v cloudu <br></br>Úlohy s vyšším výkonem a nízkou latencí<br></br>Sekundární zařízení pro zotavení po havárii |

<sup>1</sup>*Dříve označované jako 1100*.

<sup>2</sup>*Pro cloudovou vrstvu používají zařízení 8010 i 8020 službu Azure Standard Storage. Rozdíl je pouze u místní vrstvy v rámci zařízení*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Rozdíly mezi cloudovým zařízením a fyzickým zařízením

Řešení StorSimple Cloud Appliance je čistě softwarová verze StorSimple, která běží na jednom uzlu virtuálního počítače Microsoft Azure. Cloudové zařízení podporuje scénáře zotavení po havárii, ve kterých není fyzické zařízení k dispozici. Cloudové zařízení je vhodné pro použití v načítání ze záloh na úrovni položek, místní zotavení po havárii a scénáře vývoje a testování v cloudu.

#### <a name="differences-from-the-physical-device"></a>Rozdíl oproti fyzickému zařízení

V následující tabulce je uvedeno několik klíčových rozdílů mezi řešením StorSimple Cloud Appliance a fyzickým zařízením StorSimple.

|  | Fyzické zařízení | Cloudové zařízení |
| --- | --- | --- |
| **Umístění** |Nachází se v datovém centru. |Běží v Azure. |
| **Síťová rozhraní** |Má šest síťových rozhraní: DATA 0 až DATA 5. |Má pouze jedno síťové rozhraní: DATA 0. |
| **Registrace** |Registruje se v rámci kroku počáteční konfigurace. |Registrace je samostatná úloha. |
| **Šifrovací klíč dat služby** |Znovu se vygeneruje na fyzickém zařízení a cloudové zařízení se pak aktualizuje s použitím nového klíče. |Nelze znovu vygenerovat z cloudového zařízení. |
| **Podporované typy svazků** |Podporuje místně připnuté a vrstvené svazky. |Podporuje pouze vrstvené svazky. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Požadavky pro cloudové zařízení

Následující části popisují požadavky na konfiguraci pro řešení StorSimple Cloud Appliance. Před nasazením cloudového zařízení zkontrolujte aspekty zabezpečení pro používání cloudového zařízení.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Požadavky na Azure

Než zřídíte cloudové zařízení, je třeba provést následující přípravy v prostředí Azure:

* Ujistěte se, že ve svém datovém centru máte nasazené a spuštěné fyzické zařízení StorSimple řady 8000 (model 8100 nebo 8600). Zaregistrujte toto zařízení ve stejné službě Správce zařízení StorSimple, pro kterou chcete vytvořit řešení StorSimple Cloud Appliance.
* Pro cloudové zařízení [nakonfigurujte virtuální síť v Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Používáte-li službu Premium Storage, musíte vytvořit virtuální síť v oblasti Azure, která podporuje službu Premium Storage. Oblasti služby Storage úrovně Premium jsou oblasti, které odpovídají řádku pro Diskové úložiště v [seznamu služeb Azure podle oblasti](https://azure.microsoft.com/regions/services/).
* Doporučujeme použít výchozí server DNS poskytovaný platformou Azure místo zadávání vlastního názvu serveru DNS. Pokud název serveru DNS není platný nebo pokud server DNS není schopen správně přeložit IP adresy, vytvoření cloudového zařízení se nezdaří.
* Připojení point-to-site a site-to-site jsou volitelná, ale nejsou vyžadována. Pokud chcete, můžete nastavit tyto možnosti pro pokročilejší scénáře.
* Můžete ve virtuální síti vytvořit [virtuální počítače Azure](../virtual-machines/virtual-machines-windows-quick-create-portal.md) (hostitelské servery), které můžou používat svazky vystavené cloudovým zařízením. Tyto servery musí splňovat následující požadavky:

  * Být virtuální počítače Windows nebo Linux s nainstalovaným softwarem iniciátoru iSCSI.
  * Musí být spuštěné ve stejné virtuální síti jako cloudové zařízení.
  * Musí být schopné se připojit k cíli iSCSI cloudového zařízení prostřednictvím interní IP adresy cloudového zařízení.
  * Ujistěte se, že jste nakonfigurovali podporu pro přenosy iSCSI a cloudové přenosy ve stejné virtuální síti.

#### <a name="storsimple-requirements"></a>Požadavky na StorSimple

Před vytvořením cloudového zařízení aktualizujte službu Správce zařízení StorSimple následujícím způsobem:

* Přidejte [záznamy řízení přístupu](storsimple-8000-manage-acrs.md) pro virtuální počítače, které budou představovat hostitelské servery pro cloudové zařízení.
* Nastavte používání [účtu úložiště](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) ve stejné oblasti jako cloudové zařízení. Účty úložiště v jiných oblastech mohou vést k nižšímu výkonu. U cloudového zařízení můžete použít účet služby Storage úrovně Standard nebo Premium. Další informace o tom, jak vytvořit [účet služby Standard Storage](../storage/common/storage-create-storage-account.md) nebo [účet služby Premium Storage](../virtual-machines/windows/premium-storage.md)
* Pro vytvoření cloudového zařízení použijte jiný účet úložiště, než jaký se používá pro vaše data. Použití stejného účtu úložiště může vést k nižšímu výkonu.

Před zahájením se ujistěte, že máte k dispozici následující informace:

* Účet na webu Azure Portal s přihlašovacími údaji pro přístup.
* Kopii šifrovacího klíče dat služby z fyzického zařízení zaregistrovaného ve službě Správce zařízení StorSimple.

## <a name="create-and-configure-the-cloud-appliance"></a>Vytvoření a konfigurace cloudového zařízení

Před provedením těchto postupů se ujistěte, že jste splnili [požadavky pro cloudové zařízení](#prerequisites-for-the-cloud-appliance).

Provedením následujících kroků vytvořte řešení StorSimple Cloud Appliance.

### <a name="step-1-create-a-cloud-appliance"></a>Krok 1: Vytvoření cloudového zařízení

Provedením následujících kroků vytvořte řešení StorSimple Cloud Appliance.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Pokud se vytváření cloudového zařízení v tomto kroku nezdaří, je možné, že nemáte připojení k internetu. Další informace najdete v části [řešení potíží při selhání připojení k internetu](#troubleshoot-internet-connectivity-errors) během vytváření cloudového zařízení.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Krok 2: Konfigurace a registrace cloudového zařízení

Před zahájením tohoto postupu se ujistěte, že máte kopii šifrovacího klíče dat služby. Šifrovací klíč dat služby se vytvoří při registraci prvního fyzického zařízení StorSimple ve službě Správce zařízení StorSimple. Dostali jste pokyn jej uložit na bezpečném místě. Pokud nemáte kopii šifrovacího klíče dat služby, obraťte se o pomoc na podporu společnosti Microsoft.

Provedením následujících kroků nakonfigurujte a zaregistrujte řešení StorSimple Cloud Appliance.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Krok 3 (nepovinný): Úprava konfiguračních nastavení zařízení

Následující část popisuje nastavení konfigurace zařízení potřebná pro řešení StorSimple Cloud Appliance, pokud chcete použít protokol CHAP nebo StorSimple Snapshot Manager nebo změnit heslo správce zařízení.

#### <a name="configure-the-chap-initiator"></a>Konfigurace iniciátoru CHAP

Tento parametr obsahuje přihlašovací údaje, které cloudové zařízení (cíl) očekává od iniciátorů (serverů), které se pokoušejí o přístup ke svazkům. Iniciátory se při ověření identifikují vašemu zařízení uvedením uživatelského jména a hesla protokolu CHAP. Podrobný postup je uveden v části [Konfigurace CHAP pro vaše zařízení](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Konfigurace cíle CHAP

Tento parametr obsahuje přihlašovací údaje, které cloudové zařízení používá, když iniciátor s podporou protokolu CHAP žádá o vzájemné nebo obousměrné ověření. Cloudové zařízení se při ověření identifikuje iniciátoru pomocí uživatelského jména a hesla reverzního CHAP.

> [!NOTE]
> Nastavení cíle protokolu CHAP jsou globální nastavení. Pokud použijete tato nastavení, všechny svazky připojené ke cloudovému zařízení budou používat ověřování protokolem CHAP.

Podrobný postup je uveden v části [Konfigurace CHAP pro vaše zařízení](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurace hesla Snapshot Manageru zařízení StorSimple

Software Snapshot Manager zařízení StorSimple se nachází na hostiteli systému Windows a umožňuje správcům spravovat zálohy zařízení StorSimple ve formě místních a cloudových snímků.

> [!NOTE]
> Pro cloudové zařízení je hostitelem systému Windows virtuální počítač Azure.

Při konfiguraci zařízení ve StorSimple Snapshot Manageru budete vyzváni k zadání IP adresy a hesla zařízení StorSimple pro ověření úložného zařízení. Podrobný postup je uveden v části [Konfigurace hesla Snapshot Manageru zařízení StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Změna hesla správce zařízení

Pokud pro přístup ke cloudovému zařízení použijete rozhraní Windows PowerShell, bude se vyžadovat zadání hesla správce zařízení. Kvůli bezpečnosti vašich dat je před použitím cloudového zařízení nutné toto heslo změnit. Podrobný postup je uveden v části [Konfigurace hesla správce zařízení](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Vzdálené připojení ke cloudovému zařízení

Vzdálený přístup ke cloudovému zařízení prostřednictvím rozhraní Windows PowerShell není ve výchozím nastavení povolen. Vzdálenou správu musíte nejprve povolit na cloudovém zařízení a pak na klientu, který se používá pro přístup ke cloudovému zařízení.

Následující postup ve dvou krocích popisuje, jak se vzdáleně připojit ke cloudovému zařízení.

### <a name="step-1-configure-remote-management"></a>Krok 1: Konfigurace vzdálené správy

Provedením následujících kroků nakonfigurujte vzdálenou správu řešení StorSimple Cloud Appliance.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Krok 2: Vzdálený přístup ke cloudovému zařízení

Po povolení vzdálené správy na cloudovém zařízení se pomocí vzdálené komunikace Windows PowerShellu připojte k zařízení z jiného virtuálního počítače ve stejné virtuální síti. Můžete se připojit například z hostitelského virtuálního počítače, který jste nakonfigurovali a použili pro připojení k iSCSI. Ve většině nasazení otevřete veřejný koncový bod pro přístup k hostitelskému virtuálnímu počítači, který můžete použít pro přístup ke cloudovému zařízení.

> [!WARNING]
> **Pro zvýšení zabezpečení důrazně doporučujeme používat pro připojování ke koncovým bodům protokol HTTPS a po dokončení vzdálené relace PowerShellu koncové body odstranit.**

Při nastavování vzdálené komunikace pro cloudové zařízení musíte dodržovat postupy uvedené v tématu [Vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md).

## <a name="connect-directly-to-the-cloud-appliance"></a>Přímé připojení ke cloudovému zařízení

Ke cloudovému zařízení se můžete připojit také přímo. Pokud se chcete ke cloudovému zařízení připojit přímo z jiného počítače mimo virtuální síť nebo mimo prostředí Microsoft Azure, musíte vytvořit další koncové body.

Provedením následujících kroků vytvořte na cloudovém zařízení veřejný koncový bod.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Doporučujeme připojit se z jiného virtuálního počítače ve stejné virtuální síti, protože tento postup minimalizuje počet veřejných koncových bodů ve virtuální síti. V tomto případě se k virtuálnímu počítači připojte prostřednictvím relace vzdálené plochy a potom můžete provést konfiguraci tohoto virtuálního počítače pro použití stejně jako u jiných klientů systému Windows v místní síti. Nemusíte připojovat číslo veřejného portu, protože port je již znám.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Získání privátní IP adresy pro cloudové zařízení

Aby se cloudové zařízení mohlo připojit k hostitelskému serveru ve stejné virtuální síti, potřebujete interní nebo privátní IP adresu daného cloudového zařízení. Provedením následujících kroků získejte privátní IP adresu cloudového zařízení.

1. Přejděte k základnímu virtuálnímu počítači pro vaše cloudové zařízení. Virtuální počítač má stejný název jako cloudové zařízení. Přejděte do části **Všechny prostředky**, zadejte název cloudového zařízení a předplatného a jako typ vyberte virtuální počítače. V zobrazeném seznamu virtuálních počítačů vyberte a klikněte na virtuální počítač odpovídající cloudovému zařízení.

     ![Výběr virtuálního počítače pro cloudové zařízení](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Přejděte do **Nastavení > Sítě**. V pravém podokně se zobrazí privátní IP adresa cloudového zařízení. Poznamenejte si ji.

    ![Získání privátní IP adresy pro cloudové zařízení](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Práce s řešením StorSimple Cloud Appliance

Nyní, po vytvoření a konfiguraci řešení StorSimple Cloud Appliance, s ním můžete začít pracovat. Na cloudovém zařízení můžete pracovat s kontejnery svazků, svazky a zásadami zálohování stejně jako na fyzickém zařízení StorSimple. Jediným rozdílem je, že musíte zkontrolovat, jestli jste vybrali cloudové zařízení ze seznamu zařízení. Podrobné postupy různých úloh správy pro cloudové zařízení jsou popsány v tématu popisujícím [použití služby Správce zařízení StorSimple pro správu cloudového zařízení](storsimple-8000-manager-service-administration.md).

Následující části popisují některé rozdíly, na které můžete narazit při práci s cloudovým zařízením.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Údržba řešení StorSimple Cloud Appliance

Protože jde čistě o softwarové zařízení, údržba cloudového zařízení je ve srovnání s údržbou fyzického zařízení minimální.

Cloudové zařízení nelze aktualizovat. Použijte nejnovější verzi softwaru k vytvoření nového cloudového zařízení.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Účty úložiště pro cloudové zařízení

Účty úložiště se vytváří pro použití službou Správce zařízení StorSimple, cloudovým zařízením a fyzickým zařízením. Při vytváření účtů úložiště doporučujeme použít v popisném názvu identifikátor oblasti. To pomáhá zajistit konzistenci oblasti pro všechny systémové komponenty. Pro cloudové zařízení je důležité mít všechny komponenty ve stejné oblasti, aby se zabránilo problémům s výkonem.

Podrobný postup je popsán v části [Přidání účtu úložiště](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Deaktivace řešení StorSimple Cloud Appliance

Pokud deaktivujete cloudové zařízení, tato akce odstraní virtuální počítač a prostředky vytvořené při jeho zřizování. Po deaktivaci cloudového zařízení není možné ho obnovit do předchozího stavu. Před deaktivací cloudového zařízení nezapomeňte zastavit nebo odstranit klienty a hostitele, kteří na něm závisí.

Deaktivace cloudového zařízení má za následek následující akce:

* Odebere se cloudové zařízení.
* Odebere se disk operačního systému a datové disky vytvořené pro cloudové zařízení.
* Hostované služby a virtuální sítě vytvořené při zřizování zůstanou zachovány. Pokud je nepoužíváte, je nutné je odstranit ručně.
* Snímky v cloudu vytvořené pro cloudové zařízení zůstanou zachovány.

Podrobný postup je popsán v části [Deaktivace a odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).

Jakmile je cloudové zařízení zobrazeno v okně služby Správce zařízení StorSimple jako deaktivované, můžete ho odstranit ze seznamu zařízení v okně **Zařízení**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Spuštění, zastavení a restartování cloudového zařízení
Na rozdíl od fyzického zařízení StorSimple nemá řešení StorSimple Cloud Appliance žádné tlačítko pro zapnutí nebo vypnutí. Mohou však nastat situace, kdy potřebujete cloudové zařízení zastavit a restartovat.

Spuštění, zastavení a restartování cloudového zařízení můžete nejjednodušeji provést v okně služby Virtual Machines. Přejděte do služby Virtual Machines. V seznamu virtuálních počítačů identifikujte virtuální počítač odpovídající cloudovému zařízení (stejný název) a klikněte na jeho název. Když se podíváte na okno virtuálního počítače, u cloudového zařízení je uveden stav **Spuštěno**, protože se ve výchozím nastavení spustí po vytvoření. Virtuální počítač můžete kdykoli spustit, zastavit nebo restartovat.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Obnovení na výchozí hodnoty z výroby
Pokud se rozhodnete, že chcete s cloudovým zařízením začít znovu, můžete ho deaktivovat a odstranit a potom vytvořit nové.

## <a name="fail-over-to-the-cloud-appliance"></a>Převzetí služeb při selhání do cloudového zařízení
Zotavení po havárii je jedním z klíčových scénářů, pro které je řešení StorSimple Cloud Appliance určeno. Tento scénář předpokládá možnou nedostupnost fyzického zařízení StorSimple nebo celého datového centra. Naštěstí můžete použít cloudové zařízení k obnovení operací v alternativním umístění. Kontejnery svazků ze zdrojového zařízení během zotavení po havárii změní vlastnictví a jsou přeneseny do cloudového zařízení.

Požadavky pro zotavení po havárii jsou následující:

* Cloudové zařízení je vytvořeno a nakonfigurováno.
* Všechny svazky v kontejneru svazků jsou offline.
* Kontejner svazků, u kterého provádíte převzetí služeb při selhání, má přiřazený snímek v cloudu.

> [!NOTE]
> * Při použití cloudové zařízení jako sekundárního zařízení pro zotavení po havárii mějte na paměti, že zařízení 8010 má k dispozici službu Storage úrovně Standard s 30 TB úložiště a zařízení 8020 službu Storage úrovně Premium s 64 TB úložiště. Vyšší kapacita cloudového zařízení 8020 může být pro scénář zotavení po havárii vhodnější.

Podrobný postup najdete v tématu popisujícím [převzetí služeb při selhání do cloudového zařízení](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Odstranění cloudového zařízení
Pokud jste již dříve nakonfigurovali a používali řešení StorSimple Cloud Appliance, ale nyní chcete zastavit nabíhání poplatků za jeho využívání, musíte cloudové zařízení zastavit. Zastavení cloudového zařízení uvolní virtuální počítač. Tato akce zastaví nabíhání poplatků za předplatné. Poplatky za úložiště pro disk operačního systému a datové disky se ale budou účtovat i nadále.

Pokud chcete zastavit nabíhání všech poplatků, musíte cloudové zařízení odstranit. Pokud chcete odstranit zálohy vytvořené cloudovým zařízením, můžete zařízení deaktivovat nebo odstranit. Další informace naleznete v tématu [Deaktivace a odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Řešení potíží s připojením k internetu
Pokud během vytváření cloudového zařízení není k dispozici připojení k internetu, daný krok vytváření se nezdaří. Pokud chcete řešit potíže se selháním připojení k internetu, proveďte na webu Azure Portal následující kroky:

1. [Vytvořte v Azure virtuální počítač s Windows Serverem 2012](/articles/virtual-machines/windows/quick-create-portal.md). Tento virtuální počítač by měl používat stejný účet úložiště, virtuální síť a podsíť jako cloudové zařízení. Pokud již v Azure je hostitel s Windows Serverem, který používá stejný účet úložiště, virtuální síť a podsíť, můžete jej také použít pro řešení potíží s připojením k internetu.
2. Vzdáleně se přihlaste k virtuálnímu počítači, který jste vytvořili v předchozím kroku.
3. Na virtuálním počítači otevřete okno příkazového řádku (stiskněte Win + R a zadejte `cmd`).
4. V příkazovém řádku spusťte následující příkaz.

    `nslookup windows.net`
5. Pokud se příkaz `nslookup` nezdaří, pak problém s připojením k internetu brání cloudovému zařízení v registraci ve službě Správce zařízení StorSimple.
6. Proveďte požadované změny virtuální sítě a ujistěte se, že cloudové zařízení může přistupovat k webům Azure, jako je například _windows.net_.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít službu Správce zařízení StorSimple pro správu cloudového zařízení](storsimple-8000-manager-service-administration.md).
* Naučte se [obnovit svazek StorSimple ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
