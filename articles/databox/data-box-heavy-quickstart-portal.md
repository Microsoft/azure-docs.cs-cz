---
title: Rychlý start pro Microsoft Azure Data Box Heavy | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak nasadit Azure Data Box Heavy s využitím webu Azure Portal, včetně zapojení kabeláže, konfigurace a kopírování dat, která se mají nahrát Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9eda54ad23e06149910fe69ec16588f49829a5a5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122819"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Rychlý start: Nasazení Azure Data Boxu Heavy pomocí webu Azure Portal

Tento rychlý start popisuje, jak nasadit Azure Data Box Heavy pomocí webu Azure Portal. V tomto postupu najdete informace o zapojení, konfiguraci a kopírování dat do Data Boxu Heavy, aby se data nahrála do služby Azure. Rychlý start se provádí na webu Azure Portal a přes místní webové uživatelské rozhraní zařízení.

Podrobné pokyny k nasazení a sledování najdete v článku [Kurz: Objednání Azure Data Boxu Heavy](data-box-heavy-deploy-ordered.md).

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci místa instalace a služby a zařízení Data Box.

### <a name="for-installation-site"></a>Místo instalace

Než začnete, ujistěte se, že:

- Zařízení projde všemi vašimi vstupními otvory. Rozměry zařízení: šířka: 66 cm, délka: 122 cm, výška: 71 cm.
- Pokud chcete zařízení nainstalovat v jiném patře než přízemí, musíte mít možnost přenést zařízení výtahem nebo přes rampu.
- Máte dvě osoby, které budou se zařízením manipulovat. Zařízení váží přibližně 227 kg a dodává se na kolečkách.
- V datacentru máte rovnou plochu v blízkosti dostupného síťového připojení, kam je možné umístit takto velké zařízení.

### <a name="for-service"></a>Služba

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:

- Máte prostudované [bezpečnostní pokyny pro Data Box Heavy](data-box-safety.md).
- Máte k síti datacentra připojený hostitelský počítač. Data Box Heavy zkopíruje data z tohoto počítače. Na vašem hostitelském počítači musí běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
- Máte přenosný počítač s kabelem RJ-45, abyste se mohli připojit k místnímu uživatelskému rozhraní a nakonfigurovat zařízení. Pomocí přenosného počítače postupně nakonfigurujete jednotlivé uzly zařízení.
- Vaše datacentrum je připojené k vysokorychlostní síti a máte alespoň jedno připojení 10 GbE.
- Pro každý uzel zařízení potřebujete jeden kabel s rychlostí 40 GB/s nebo jeden kabel s rychlostí 10 GB/s. Zvolte kabely, které jsou kompatibilní se síťovým rozhraním Mellanox MCX314A-BCCT:
    - V případě kabelu s rychlostí 40 GB/s musí na konci kabelu na straně zařízení být QSFP+.
    - V případě kabelu s rychlostí 10 GB/s potřebujete kabel SFP+, který se na jednom konci připojí k přepínači s rychlostí 10 GB/s, s adaptérem QSFP+ na SFP+ (neboli adaptérem QSA) na druhém konci, který se připojí k zařízení.
- Napájecí kabely jsou přiložené v zásobníku na zadní straně zařízení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Objednání

Tento krok trvá přibližně 5 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box.
2. Vyberte stávající předplatné, ve kterém je tato služba povolená, a zvolte typ přenosu **Import**. Do pole **Zdrojová země** zadejte zemi, kde se data nacházejí, a v poli **Cílová oblast Azure** zvolte oblast, kam se mají data přenést.
3. Vyberte **Data Box Heavy**. Maximální využitelná kapacita je 770 TB a v případě většího množství dat můžete zadat více objednávek.
4. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky je zařízení připravené k odeslání.

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Zapojení kabeláže a připojení k zařízení

Jakmile zkontrolujete požadavky, zapojíte kabeláž zařízení a připojíte se k němu.

::: zone-end

## <a name="cable-for-power"></a>Zapojení kabeláže k napájení

Tento krok zabere asi 5 minut.

Když obdržíte Data Box Heavy, podle následujících pokynů zapojte kabeláž zařízení k napájení a zapněte ho.

1. Pokud vidíte, že je zařízení poškozené nebo že s ním někdo manipuloval, nepokračujte. Požádejte podporu Microsoftu o dodání náhradního zařízení.
2. Přesuňte zařízení na místo instalace a zablokujte zadní kolečka.
3. Připojte všechny čtyři napájecí kabely na zadní straně zařízení k napájecím zdrojům.
4. Pomocí tlačítek napájení na přední straně zapněte uzly zařízení.

## <a name="cable-first-node-for-network"></a>Připojení prvního uzlu k síti

Tento krok trvá 10 až 15 minut.

1. Pomocí síťového kabelu RJ-45 kategorie 6 připojte hostitelský počítač k portu pro správu (MGMT) na zařízení.
2. Pomocí měděného kabelu Twinax QSFP+ připojte alespoň jedno síťové rozhraní s rychlostí 40 Gb/s (tomu se dává přednost před rozhraním s rychlostí 1 Gb/s), DATA 1 nebo DATA 2 pro data. Pokud používáte přepínač s rychlostí 10 GB/s, použijte k připojení síťového rozhraní s rychlostí 40 GB/s pro data měděný kabel Twinax SFP+ s adaptérem QSFP+ na SFP+ (adaptér QSA).
3. K zařízení připojte kabely, jak vidíte níže.  

    ![Data Box Heavy se zapojenou kabeláží](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurace prvního uzlu

Tento krok trvá 5 až 7 minut.

1. Pokud chcete získat heslo zařízení, přejděte na webu [Azure Portal](https://portal.azure.com) na **Obecné > Podrobnosti o zařízení**. Pro oba uzly zařízení se používá stejné heslo.
2. Na adaptéru sítě Ethernet na počítači, pomocí kterého se připojujete k Data Boxu Heavy, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. Přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`. Připojení zařízení po zapnutí může trvat až 5 minut.
3. Přihlaste se pomocí hesla z webu Azure Portal. Zobrazí se vám chyba s informacemi k potížím s certifikátem zabezpečení webu. Postupujte podle pokynů pro konkrétní prohlížeč a přejděte tak na webovou stránku.
4. Standardně je pro nastavení sítě pro rozhraní (s výjimkou rozhraní MGMT) nakonfigurovaný protokol DHCP. V případě potřeby můžete tato rozhraní nakonfigurovat jako statická a zadat IP adresu.

## <a name="cable-and-configure-the-second-node"></a>Zapojení kabeláže a konfigurace druhého uzlu

Tento krok trvá 15 až 20 minut.

Pomocí stejného postupu jako u prvního uzlu zapojte kabeláž druhého uzlu zařízení a nakonfigurujte ho.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopírování dat

Doba trvání této operace závisí na množství dat a rychlosti sítě, přes kterou se data kopírují.
 
1. Paralelně zkopírujte data na oba uzly zařízení s využitím obou datových rozhraní s rychlostí 40 GB/s.

    - Pokud používáte hostitele Windows, použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, jako je třeba [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
    - Pro hostitele systému souborů NFS použijte ke kopírování dat příkaz `cp` nebo `rsync`.
2. Připojte se ke sdíleným složkám na zařízení s použitím cesty: `\\<IP address of your device>\ShareName`. Pokud chcete získat přihlašovací údaje ke sdílené složce, v místním webovém uživatelském rozhraní Data Boxu Heavy přejděte na stránku **Připojit a kopírovat**.
3. Název sdílené složky a složky a data musí vyhovovat pokynům popsaným v tématu [Omezení služby Azure Storage a Data Boxu Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Příprava k odeslání

Doba trvání této operace závisí na množství dat.

1. Jakmile se dokončí kopírování dat bez jakýchkoli chyb, v místním webovém uživatelském rozhraní přejděte na stránku **Příprava k odeslání** a zahajte přípravu k odeslání.
2. Po úspěšném dokončení **přípravy k odeslání** na obou uzlech pomocí místního webového uživatelského rozhraní zařízení vypněte.

## <a name="ship-to-azure"></a>Odeslání do Azure

Tato operace trvá asi 15 až 20 minut.

1. Odpojte kabely a vraťte je do zásobníku na zadní straně zařízení.
2. Naplánujte vyzvednutí místní přepravní službou.
3. Spojte se s [provozním oddělením Data Boxu](mailto:DataBoxOps@microsoft.com), informujte je o vyzvednutí a získejte od nich zpáteční expediční štítek.
4. Zpáteční expediční štítek by měl být vidět na předním panelu zařízení.

## <a name="verify-data"></a>Ověření dat

Doba trvání této operace závisí na množství dat.

1. Když se zařízení Data Box Heavy připojí k síti v datacentru Azure, data se automaticky nahrají do Azure.
2. Jakmile se kopírování dat dokončí, služba Data Box vám přes web Azure Portal pošle oznámení.

    1. Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
    2. Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Dokončení tohoto kroku trvá 2 až 3 minuty.

- Před zpracováním objednávky můžete objednávku Data Boxu Heavy zrušit na webu Azure Portal. Po zpracování objednávky už se objednávka zrušit nedá. Průběh objednávky bude pokračovat až do fáze Dokončeno. Pokud chcete objednávku zrušit, přejděte do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

- Jakmile se na webu Azure Portal objeví stav **Dokončeno** nebo **Zrušeno**, můžete objednávku odstranit. Pokud chcete odstranit objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili Data Box Heavy, který vám má pomoci s importem dat do Azure. Další informace o správě Azure Data Boxu Heavy najdete v následujícím kurzu:

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Data Boxu Heavy](data-box-portal-admin.md)

::: zone-end