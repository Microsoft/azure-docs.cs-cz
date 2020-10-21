---
title: 'Rychlý start: Export dat pro Microsoft Azure Data Box'
description: Přečtěte si, jak rychle vyexportovat data Azure Data Boxu pomocí webu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 789bd2f62673e7faf562d3a407bf2f0a4fd861bf
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125488"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Rychlý start: Začínáme s Azure Data Boxem pro export dat z Azure

Tento rychlý start popisuje postup při exportu data z Azure do vašeho umístění s využitím webu Azure Portal. Uvedené kroky zahrnují zapojení kabeláže, konfiguraci a kopírování dat z Azure. Rychlý start se provádí na webu Azure Portal a přes místní webové uživatelské rozhraní zařízení.

Podrobné pokyny k nasazení a sledování najdete v článku [Kurz: Vytvoření objednávky exportu pro Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Požadavky

Než začnete:

* Typ předplatného, které používáte pro službu Data Box, musí být jeden z následujících:
  * Smlouva Microsoft Enterprise (EA). Další informace o [předplatných se smlouvou Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Cloud Solution Provider (CSP). Další informace o [programu Azure CSP](/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure Sponsorship. Získejte další informace o [programu Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Pokud chcete vytvořit objednávku Data Boxu, potřebujete mít u předplatného přístup vlastníka nebo přispěvatele.
* Přečtěte si [bezpečnostní pokyny pro Data Box](data-box-safety.md).
* Máte zařízení Azure Data Box pro přesun dat do vašeho umístění. Hostitelský počítač musí splňovat tyto požadavky:
  * Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
  * Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte připojení 10 GbE, je možné použít datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování.
* Data Box je potřeba umístit na rovnou plochu. Pokud chcete zařízení umístit na standardní polici v racku, potřebujete v racku datového centra slot 7U. Zařízení můžete v racku umístit na plocho nebo na výšku.
* Pro připojení Data Boxu k hostitelském počítači je potřeba pořídit následující kabely.
  * Dva měděné kabely 10 GbE SFP+ Twinax (pro síťová rozhraní DATA 1, DATA 2)
  * Jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní správy (MGMT))
  * Jeden síťový kabel RJ-45 kategorie 6A NEBO jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní DATA 3 konfigurované jako 10 Gb/s a 1 Gb/s v tomto pořadí)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Objednání

Tento krok trvá přibližně 10 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box.
2. Vyberte stávající předplatné, ve kterém je tato služba povolená, a jako typ přenosu zvolte **Export do Azure**. Do pole **Zdrojová oblast Azure** zadejte oblast, kde se data nacházejí, a v poli **Cílová země** zvolte zemi, kam se mají data přenést.
3. Vyberte **Data Box**. Maximální využitelná kapacita je 80 TB a v případě většího množství dat můžete zadat více objednávek.
4. Vyberte **Add storage account and export type** (Přidat účet úložiště a typ exportu) a potom **vyberte možnost Export**.
5. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky je zařízení připravené k odeslání.

## <a name="cable"></a>Kabel

Tento krok trvá přibližně 10 minut.

Když obdržíte Data Box, zapojte kabely a zařízení připojte a zapněte podle následujících pokynů. Tento krok zabere asi 10 minut.

1. Pokud vidíte, že je zařízení poškozené nebo že s ním někdo manipuloval, nepokračujte. Požádejte podporu Microsoftu o dodání náhradního zařízení.
2. Před zapojením zařízení zkontrolujte, že máte následující kabely:

   * Uzemněný napájecí kabel 10 A nebo více s konektorem IEC60320 C-13 na jednom konci pro připojení k zařízení (součástí dodávky)
   * Jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní správy (MGMT))
   * Dva měděné kabely 10 GbE SFP+ Twinax (pro síťová rozhraní DATA 1, DATA 2 10 Gb/s)
   * Jeden síťový kabel RJ-45 kategorie 6A NEBO jeden síťový kabel RJ-45 kategorie 6 (pro síťové rozhraní DATA 3 konfigurované jako 10 Gb/s a 1 Gb/s v tomto pořadí)

3. Zařízení vyjměte a položte na rovnou plochu.

4. K zařízení připojte kabely, jak vidíte níže.  

    ![Zapojená kabeláž propojovacího rozhraní zařízení Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. K zařízení připojte napájecí kabel.
    2. Pomocí síťového kabelu RJ-45 kategorie 6 připojte hostitelský počítač k portu MGMT na zařízení.
    3. Pomocí měděného kabelu SFP+ Twinax připojte alespoň jedno síťové rozhraní s rychlostí 10 Gb/s (tomu se dává přednost před rozhraním s rychlostí 1 Gb/s), DATA 1 nebo DATA 2 pro data.
    4. Zapněte zařízení. Tlačítko napájení je na předním panelu zařízení.

## <a name="connect"></a>Připojit

Tento krok trvá 5 až 7 minut.

1. Pokud chcete získat heslo zařízení, přejděte na webu [Azure Portal](https://portal.azure.com) na **Obecné** > **Podrobnosti o zařízení**.
2. Na adaptéru sítě Ethernet na počítači, pomocí kterého se připojujete k Data Boxu, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0. Přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`. Připojení zařízení po zapnutí může trvat až 5 minut.
3. Přihlaste se pomocí hesla z webu Azure Portal. Zobrazí se vám chyba indikující potíže s certifikátem zabezpečení webu. Postupujte podle pokynů pro konkrétní prohlížeč a přejděte tak na webovou stránku.
4. Standardně je pro nastavení sítě pro datové rozhraní 10 Gb/s (nebo 1 Gb/s) nakonfigurovaný protokol DHCP. V případě potřeby můžete toto rozhraní nakonfigurovat jako statické a zadat IP adresu.

## <a name="copy-data"></a>Kopírování dat

Doba trvání této operace závisí na množství dat a rychlosti sítě.

1. Pokud používáte klienta pro Windows, použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, jako je třeba Robocopy. Pro hostitele systému souborů NFS použijte ke kopírování dat příkaz `cp` nebo `rsync`. Další informace o tom, jak použít Robocopy ke kopírování dat, získáte v tématu [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
2. Připojte se ke sdíleným složkám zařízení a začněte kopírovat data na hostitelský počítač.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Odeslání do Azure

Tato operace trvá asi 10–15 minut.

1. Přejděte na stránku **Prepare to ship** (Připravit k odeslání) v místním webovém uživatelském rozhraní a zahajte přípravu k odeslání.
2. Z místního webového uživatelského rozhraní vypněte zařízení. Odpojte od zařízení kabely.
3. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
4. Na displeji E-ink byste měli vidět expediční štítek pro vrácení. Pokud se štítek na displeji E-ink nezobrazuje, přejděte na webu Azure Portal na **Přehled** > **Stáhnout expediční štítek**. Stáhněte si expediční štítek a připevněte ho na zařízení.
5. Pokud vracíte zařízení, naplánujte vyzvednutí místní přepravní službou.
6. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Dokončení tohoto kroku trvá 2 až 3 minuty.

* Před zpracováním objednávky můžete objednávku Data Boxu zrušit na webu Azure Portal. Po zpracování se už objednávka zrušit nedá. Průběh objednávky bude pokračovat až do fáze Dokončeno. Pokud chcete objednávku zrušit, přejděte do části **Přehled** a na panelu příkazů vyberte **Zrušit**.

* Jakmile se na webu Azure Portal objeví stav **Dokončeno** nebo **Zrušeno**, můžete objednávku odstranit. Pokud chcete odstranit objednávku, přejděte do části **Přehled** a na panelu příkazů vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili objednávku exportu z Azure do Azure Data Boxu. Další informace o správě Azure Data Boxu získáte v následujícím kurzu:

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Data Boxu](data-box-portal-admin.md)