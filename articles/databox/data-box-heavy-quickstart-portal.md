---
title: Rychlý start pro Microsoft Azure Data Box těžkých | Dokumentace Microsoftu
description: Zjistěte, jak rychle nasadit váš Azure Data Box náročné na webu Azure portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 930da7367b3f5d7e20617afedef007efea97c51a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518579"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Rychlý start: Nasazení Azure Data Box náročné pomocí webu Azure portal

Tento rychlý start popisuje způsob nasazení Azure Data Box náročné pomocí webu Azure portal. Kroky zahrnují zapojení, konfigurace a kopírování dat do Data Box náročné tak, aby se odešle do služby Azure. Rychlý start se provádí na webu Azure Portal a přes místní webové uživatelské rozhraní zařízení.

Pro podrobné podrobný postup nasazení a pokyny pro sledování, přejděte na [kurzu: Pořadí Azure Data Box náročná na výkon](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení zajistěte splnění následujících požadavků konfigurace pro instalaci lokality, služba Data Box a zařízení.

### <a name="for-installation-site"></a>Pro instalaci lokality

Než začnete, ujistěte se, že:

- Zařízení můžete přizpůsobit prostřednictvím všech entryways. Dimenze zařízení jsou: šířka: 26" Délka: 48" height: 28”.
- Pokud budete chtít nainstalovat floor, než můžete podílet máte přístup pro zařízení prostřednictvím hodnocení nebo doběhu.
- Máte dva uživatelé ke zpracování zařízení. Zařízení váží přibližně ~ 500 lbs. a pochází na kola.
- Máte lokalitu bez stromové struktury v datovém centru s blízkosti dostupné síťové připojení, který zvládne zařízení s nároky na toto místo.

### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:

- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Předplatné, můžete použít pro službu Data Box je [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview), nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Máte přístup přispěvatele nebo vlastníka k předplatnému na vytvoření náročné pole Data objednávky.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:

- Můžete si [bezpečnostní pokyny pro vaše Data Box náročné](data-box-safety.md).
- Máte hostitelský počítač připojený k síti datového centra. Data Box náročné zkopíruje data z tohoto počítače. Musíte spustit hostitelský počítač [s podporovaným operačním systémem](data-box-heavy-system-requirements.md).
- Máte přenosném počítači pomocí kabelu RJ-45 kategorie pro připojení k místní uživatelského rozhraní a konfigurace zařízení. Pomocí přenosný počítač nakonfigurovat každý uzel zařízení jen jednou.
- Vaše datové centrum obsahuje vysokorychlostní síťové a vy musíte splnit aspoň jednu 10 GbE připojení.
- Budete potřebovat jeden kabel 40 GB/s nebo kabel 10 GB/s na jeden uzel zařízení. Zvolte kabely, které jsou kompatibilní s Mellanox MCX314A-BCCT síťové rozhraní:
    - Pro kabel 40 GB/s musí být QSFP + zařízení konec kabelu.
    - Kabel 10 GB/s je nutné kabel SFP +, která zpřístupní 10 G přepínač na jednom konci a QSFP + SFP + adaptéru (nebo adaptér podmínky) end, který se připojuje k zařízení.
- Napájecích kabelů jsou zahrnuty v hlavním zádi zařízení.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="order"></a>Objednání

Tento krok trvá přibližně 5 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box.
2. Vyberte stávající předplatné, ve kterém je tato služba povolená, a zvolte typ přenosu **Import**. Do pole **Zdrojová země** zadejte zemi, kde se data nacházejí, a v poli **Cílová oblast Azure** zvolte oblast, kam se mají data přenést.
3. Vyberte **Data Box náročná na výkon**. Můžete vytvořit více objednávek pro větší velikosti dat maximální využitelné kapacity je 770 TB.
4. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky je zařízení připravené k odeslání.

## <a name="cable-for-power"></a>Kabel výkon

Tento krok trvá přibližně 5 minut.

Když obdržíte náročné pole dat, proveďte následující kroky k zapojení zařízení pro výkon a zapněte zařízení.

1. Pokud vidíte, že je zařízení poškozené nebo že s ním někdo manipuloval, nepokračujte. Požádejte podporu Microsoftu o dodání náhradního zařízení.
2. Přesunout zařízení k instalaci lokality a uzamčení zadní kol.
3. Všechny čtyři napájecích kabelů připojení k zdroji napájení zádi zařízení.
4. Pomocí tlačítka napájení v rovině front-zapnutí zařízení uzly.

## <a name="cable-first-node-for-network"></a>První uzel kabelové sítě

Tento krok trvá asi 10 až 15 minut.

1. Pomocí síťového kabelu RJ-45 kategorie 6 připojte hostitelský počítač k portu pro správu (MGMT) na zařízení.
2. Použití kabelu Twinax QSFP + mědi připojte alespoň jedno 40 GB/s (upřednostňováno více než 1 GB/s) síťové rozhraní DATA 1 nebo 2 DATA pro data. Pokud používáte přepínač 10 GB/s, pomocí kabelu Twinax SFP + měděného a QSFP + SFP + adaptér (adaptér podmínky) připojte 40 GB/s síťové rozhraní pro data.
3. K zařízení připojte kabely, jak vidíte níže.  

    ![Data Box náročné zapojené](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurace první uzel

Tento krok trvá 5 až 7 minut.

1. Pokud chcete získat heslo zařízení, přejděte na webu [Azure Portal](http://portal.azure.com) na **Obecné > Podrobnosti o zařízení**. Stejné heslo se používá pro oba uzly zařízení.
2. Adaptér sítě Ethernet na počítači, který používáte pro připojení k datové pole v případě velkého přiřadíte statické IP adresy 192.168.100.5 a podsítě 255.255.255.0. Přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`. Připojení zařízení po zapnutí může trvat až 5 minut.
3. Přihlaste se pomocí hesla z webu Azure Portal. Zobrazí se vám chyba s informacemi k potížím s certifikátem zabezpečení webu. Postupujte podle pokynů pro konkrétní prohlížeč a přejděte tak na webovou stránku.
4. Ve výchozím nastavení jsou nastavení síťového rozhraní (s výjimkou MGMT) nakonfigurované jako DHCP. V případě potřeby můžete nakonfigurovat tato rozhraní jako statické a zadejte IP adresu.

## <a name="cable-and-configure-the-second-node"></a>Zapojení a konfiguraci druhého uzlu

Tento krok trvá přibližně 15 až 20 minut.

Postupujte podle kroků použitá pro první uzel k zapojení a konfiguraci druhého uzlu na zařízení.  

## <a name="copy-data"></a>Kopírování dat

Čas k dokončení této operace závisí na velikost vašich dat a rychlosti sítě nad tím, které se kopírují data.
 
1. Kopírování dat do obou zařízení uzlů pomocí obou 40 GB/s dat rozhraní paralelně.

    - Pokud pomocí hostitele Windows, použijte nástroj pro kopírování souborů kompatibilní SMB, jako [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Pro hostitele systému souborů NFS použijte ke kopírování dat příkaz `cp` nebo `rsync`.
2. Připojte se ke sdíleným složkám na zařízení prostřednictvím dané cesty:`\\<IP address of your device>\ShareName`. Chcete-li získat přihlašovací údaje pro přístup sdílené složky, přejděte na **Connect & copy** stránky v místního webového uživatelského rozhraní náročné pole Data.
3. Ujistěte se, že sdílené složky a názvy složek a dat podle pokynů popsaných v [omezení služby Azure Storage a Data Box náročné](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Příprava k odeslání

Doba trvání této operace závisí na množství dat.

1. Až se kopírování dat dokončí bez chyb, přejděte do **přípravu k odeslání** stránce v místním webovém uživatelském rozhraní a zahajte přípravu na odeslání.
2. Po **přípravu k odeslání** má byla úspěšně dokončena na obou uzlech, vypněte zařízení z místního webového uživatelského rozhraní.

## <a name="ship-to-azure"></a>Odeslání do Azure

Tato operace trvá přibližně 15 až 20 minut.

1. Odeberte kabely a vrátit je do zásobníku zádi zařízení.
2. Vyzvednutí naplánujte s vaší místní operátora.
3. Oslovení [operace s daty pole](mailto:DataBoxOps@microsoft.com) informovat o vyzvednutí a získat zpětný Expediční štítek.
4. Zpětný Expediční štítek musí být viditelný na přední panel vymazat zařízení.

## <a name="verify-data"></a>Ověření dat

Doba trvání této operace závisí na množství dat.

1. Když zařízení Data Box náročné je připojen k síti datového centra Azure, data se automaticky uloží do Azure.
2. Služba data Box vás upozorní, že se kopírování dat dokončí prostřednictvím webu Azure portal.

    1. Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
    2. Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Dokončení tohoto kroku trvá 2 až 3 minuty.

- Data Box náročné pořadí na webu Azure Portal můžete zrušit před zpracováním pořadí. Po zpracování objednávky už se objednávka zrušit nedá. Průběh objednávky bude pokračovat až do fáze Dokončeno. Pokud chcete objednávku zrušit, přejděte do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

- Jakmile se na webu Azure Portal objeví stav **Dokončeno** nebo **Zrušeno**, můžete objednávku odstranit. Pokud chcete odstranit objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili pole Data náročné umožňující import dat do Azure. Další informace o Azure Data Box náročné správě, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Pomocí webu Azure portal ke správě dat pole náročné](data-box-portal-admin.md)
