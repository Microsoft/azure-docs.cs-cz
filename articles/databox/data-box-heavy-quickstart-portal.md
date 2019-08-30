---
title: Rychlý Start pro Microsoft Azure Data Box Heavy | Microsoft Docs
description: Naučte se rychle nasadit Azure Data Box Heavy v Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 433c3ac08d8d7d8158d4cc958979782e3b3192e1
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164417"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Rychlý start: Nasazení Azure Data Box Heavy pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit Azure Data Box Heavy pomocí Azure Portal. Tento postup zahrnuje způsob, jakým můžete Data Box Heavy kabelem, nakonfigurovat a kopírovat data, aby se načetla do Azure. Rychlý start se provádí na webu Azure Portal a přes místní webové uživatelské rozhraní zařízení.

Podrobné pokyny k [nasazení a sledování najdete v tématu Kurz: Azure Data Box Heavy objednávky](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci pro instalační lokalitu, Data Box službu a zařízení.

### <a name="for-installation-site"></a>Pro instalační Web

Než začnete, ujistěte se, že:

- Zařízení se může vejít do všech entryways. Rozměry zařízení jsou: width: 26 "Délka: 48 "height: 28 ".
- Máte přístup k zařízení prostřednictvím výtahu nebo rampy, pokud plánujete instalaci na jinou podlahovou desku, než je podlahová plocha.
- Máte dvě osoby, které zařízení zpracovávají. Zařízení se zaváží přibližně po 500 kg. a přichází na kolech.
- Máte plochý web v datacentru s blízkým připojením k dostupnému síťovému připojení, které se může na zařízení dopracovat s těmito nároky.

### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:

- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Předplatné, které používáte pro službu Data Box, je [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- K předplatnému máte přístup vlastníka nebo přispěvatele, abyste mohli vytvořit objednávku Data Box Heavy.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:

- Prozkoumali jste [pokyny pro zabezpečení data box Heavy](data-box-safety.md).
- Máte hostitelský počítač připojený k síti datacenter. Data Box Heavy se zkopírují data z tohoto počítače. Na hostitelském počítači musí běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
- Máte přenosný počítač s kabelem RJ-45 pro připojení k místnímu uživatelskému rozhraní a konfiguraci zařízení. Pomocí přenosného počítače nakonfigurujete každý uzel zařízení jednou.
- Vaše datacentrum má vysokorychlostní síť a máte minimálně 1 10 GbE připojení.
- K jednomu uzlu zařízení potřebujete kabel 1 40-GB nebo kabel s rychlostí 10 GB/s. Výběr kabelů, které jsou kompatibilní se síťovým rozhraním Mellanox MCX314A-BCCT:
    - Pro kabel 40 GB/s musí být zařízení zakončené kabelem QSFP +.
    - U kabelu s rychlostí 10 GB/s je potřeba kabel SFP +, který se na jednom konci připojí k přepínači 10 G, a to pomocí QSFP + až SFP + adapteru (nebo adaptéru QUALIFIED Security ASSESSOR) pro konec, který se připojí k zařízení.
- Napájecí kabely jsou součástí zásobníku na zadní straně zařízení.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Pořadí

Tento krok trvá přibližně 5 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box.
2. Vyberte stávající předplatné, ve kterém je tato služba povolená, a zvolte typ přenosu **Import**. Do pole **Zdrojová země** zadejte zemi, kde se data nacházejí, a v poli **Cílová oblast Azure** zvolte oblast, kam se mají data přenést.
3. Vyberte **data box Heavy**. Maximální použitelná kapacita je 770 TB a můžete vytvořit více objednávek pro větší velikosti dat.
4. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky je zařízení připravené k odeslání.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Připojte se k zařízení přes kabel

Po zkontrolování požadavků budete kabelem a připojením k vašemu zařízení.

::: zone-end

## <a name="cable-for-power"></a>Kabel pro napájení

Tento krok trvá asi 5 minut.

Když obdržíte Data Box Heavy, proveďte následující kroky, které zařízení Zapojte k napájení a zapněte zařízení.

1. Pokud vidíte, že je zařízení poškozené nebo že s ním někdo manipuloval, nepokračujte. Požádejte podporu Microsoftu o dodání náhradního zařízení.
2. Přesuňte zařízení do instalační lokality a zamkněte zadní kola.
3. Připojte všechny čtyři napájecí kabely k napájení na zadní straně zařízení.
4. Pomocí tlačítek napájení v přední rovině zapněte uzly zařízení.

## <a name="cable-first-node-for-network"></a>Zapojte se do prvního uzlu sítě.

Dokončení tohoto kroku trvá přibližně 10-15 minut.

1. Pomocí síťového kabelu RJ-45 kategorie 6 připojte hostitelský počítač k portu pro správu (MGMT) na zařízení.
2. Použijte Twinax QSFP + Copper kabel k připojení minimálně 1 40 GB/s (upřednostňované přes 1 GB/s) síťového rozhraní, DATA 1 nebo DATA 2 pro data. Pokud používáte přepínač s rychlostí 10 GB/s, použijte Twinax SFP + Copper kabel s QSFP + a SFP + Adapter (adaptér QUALIFIED Security ASSESSOR) k připojení síťového rozhraní 40-GB/s pro data.
3. K zařízení připojte kabely, jak vidíte níže.  

    ![Data Box Heavy kabelové](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurovat první uzel

Tento krok trvá 5 až 7 minut.

1. Pokud chcete získat heslo zařízení, přejděte na webu [Azure Portal](https://portal.azure.com) na **Obecné > Podrobnosti o zařízení**. Pro oba uzly zařízení se používá stejné heslo.
2. Přiřaďte ke adaptéru Ethernet na počítači, který používáte pro připojení k Data Box Heavy statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. Přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`. Připojení zařízení po zapnutí může trvat až 5 minut.
3. Přihlaste se pomocí hesla z webu Azure Portal. Zobrazí se vám chyba s informacemi k potížím s certifikátem zabezpečení webu. Postupujte podle pokynů pro konkrétní prohlížeč a přejděte tak na webovou stránku.
4. Ve výchozím nastavení jsou nastavení sítě pro rozhraní (s výjimkou správy) nakonfigurovaná jako DHCP. V případě potřeby můžete tato rozhraní nakonfigurovat jako statickou a zadat IP adresu.

## <a name="cable-and-configure-the-second-node"></a>Kabely a konfigurace druhého uzlu

Dokončení tohoto kroku trvá přibližně 15-20 minut.

Použijte postup, který se použije pro první uzel ke kabelovému a konfiguraci druhého uzlu na zařízení.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopírovat data

Čas k dokončení této operace závisí na velikosti dat a rychlosti sítě, přes kterou se zkopírují data.
 
1. Zkopírujte data do uzlů zařízení souběžně pomocí obou datových rozhraní 40-GB.

    - Pokud používáte hostitele Windows, použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB [](https://technet.microsoft.com/library/ee851678.aspx), jako je například Robocopy.
    - Pro hostitele systému souborů NFS použijte ke kopírování dat příkaz `cp` nebo `rsync`.
2. Připojte se ke sdíleným složkám na zařízení pomocí cesty`\\<IP address of your device>\ShareName`:. Přihlašovací údaje pro přístup ke sdílené složce získáte tak, že přejdete na stránku **připojit & kopírování** v místním webovém uživatelském rozhraní data box Heavy.
3. Ujistěte se, že názvy sdílených složek a složek a data následují podle pokynů popsaných v [omezeních služby Azure Storage a data box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Připravit k odeslání

Doba trvání této operace závisí na množství dat.

1. Po dokončení kopírování dat bez chyb můžete přejít na stránku **Příprava k odeslání** v místním webovém uživatelském rozhraní a zahájit přípravu expedice.
2. Po úspěšném dokončení **Příprava k odeslání** v obou uzlech vypněte zařízení z místního webového uživatelského rozhraní.

## <a name="ship-to-azure"></a>Odeslání do Azure

Dokončení této operace trvá přibližně 15-20 minut.

1. Odeberte kabely a vraťte je do zásobníku na zadní straně zařízení.
2. Naplánujte vyzvednutí pomocí svého regionálního dopravce.
3. Přihlaste se k [data box operací](mailto:DataBoxOps@microsoft.com) , abyste informovali o vyzvednutí a získali expediční štítek s návratem.
4. Popisek pro návrat z expedice by měl být viditelný na front-Clear panelu zařízení.

## <a name="verify-data"></a>Ověření dat

Doba trvání této operace závisí na množství dat.

1. Když je zařízení Data Box Heavy připojené k síti datacenter Azure, data se automaticky nahrávají do Azure.
2. Služba Data Box vás upozorní, že se kopie dat dokončuje prostřednictvím Azure Portal.

    1. Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
    2. Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Dokončení tohoto kroku trvá 2 až 3 minuty.

- Pořadí Data Box Heavy v Azure Portal můžete před zpracováním objednávky zrušit. Po zpracování objednávky už se objednávka zrušit nedá. Průběh objednávky bude pokračovat až do fáze Dokončeno. Pokud chcete objednávku zrušit, přejděte do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

- Jakmile se na webu Azure Portal objeví stav **Dokončeno** nebo **Zrušeno**, můžete objednávku odstranit. Pokud chcete odstranit objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili Data Box Heavy, která vám pomůžou importovat data do Azure. Pokud se chcete dozvědět víc o správě Azure Data Box Heavy, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Správa Data Box Heavy pomocí Azure Portal](data-box-portal-admin.md)

::: zone-end
