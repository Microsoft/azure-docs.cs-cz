---
title: Informace o integraci Cisco ISE pxGrid
description: Přemostění možností programu Defender pro IoT s Cisco ISE pxGrid poskytuje týmům zabezpečení, které nepředchůdce v rámci podnikového ekosystému.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784080"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Informace o integraci Cisco ISE pxGrid

Defender pro IoT přináší jedinou platformu ICS a IoT kyberbezpečnosti vytvořenou odborníky z oblasti Blue-tým se záznamem sledování, který chrání kritickou národní infrastrukturu a jedinou platformou s patentovou analýzou ochrany před internetovými útoky a strojovým učením. Defender pro IoT poskytuje:

- Okamžité přehledy o zařízeních ICS, chybách zabezpečení a známých a nenulových hrozbách.

- Bohaté informace o protokolech, zařízeních, aplikacích a jejich chování s obsáhlou integrovanou službou ICS

- Automatizovaná technologie ICS pro vytváření hrozeb pro předpověď nejpravděpodobnějších cest k cíleným útokům ICS prostřednictvím proprietárních analýz.

## <a name="powerful-device-visibility-and-control"></a>Výkonná viditelnost a řízení zařízení

Integrace programu Defender for IoT s Cisco ISE pxGrid poskytuje novou úroveň centralizované viditelnosti, monitorování a řízení pro krajinu.

Tyto přemostění platforem umožňují automatizovanou viditelnost zařízení a ochranu před nedosažitelnými zařízeními ICS a IIoT.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Funkce ICS a IIoT, viditelnost zařízení: vyčerpávající a hluboko

Přidaný program Defender pro technologie IoT zajišťuje komplexní a rozsáhlou službu sdílení a správu zařízení s IIoT a správu inventáře pro podniková data.

V reálném čase se aktualizují typy zařízení, výrobci, otevřené porty, sériová čísla, revize firmwaru, IP adresy a informace o adrese MAC. Defender pro IoT může dál vylepšit viditelnost, zjišťování a analýzu z tohoto směrného plánu integrací s důležitými podnikovými zdroji dat. Například CMDBs, DNS, brány firewall a webová rozhraní API.

Kromě toho platforma Defender for IoT kombinuje pasivní monitorování a volitelné techniky selektivního zjišťování, aby poskytovala nejpřesnější a detailní inventarizaci zařízení v průmyslových a důležitých organizacích infrastruktury.

### <a name="bridged-capabilities"></a>Možnosti mostu

Přemostění těchto schopností pomocí Cisco ISE pxGrid zajišťuje týmům pro zabezpečení, které nepatří do ekosystému podnikového ekosystému.

Bezproblémová a robustní integrace s Cisco ISE pxGrid zajišťuje nezjištění nenalezeného zařízení a nezmizí žádné informace o zařízení.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Ukázka kategorií koncového bodu OUI.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Ukázkové koncové body v systému":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Snímek obrazovky s atributy umístěnými v systému":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Použití pokrytí případu: zásady ISE založené na Defenderu pro atributy IoT

Využijte výkonné zásady ISE založené na programu Defender pro IoT Learning pro zpracování požadavků na použití ICS a IoT.

Práce se zásadami vám umožní uzavřít cyklus zabezpečení a zajistit, aby síť vyhovovala v reálném čase.

Zákazníci můžou například pomocí programu Defender pro IoT ICS a atributů IoT vytvářet zásady, které zpracovávají následující případy použití:

- Vytvořte zásady autorizace, které umožní, aby známá a autorizovaná zařízení byla v citlivé zóně na základě povolených atributů – například konkrétní verze firmwaru pro Rockwell Automation PLCs.

- Upozorněte týmy zabezpečení při zjištění zařízení ICS v zóně, která není typu OT.

- Opravte počítače pomocí zastaralých nebo nekompatibilních dodavatelů.

- Umístit do karantény a blokovat zařízení podle potřeby.

- Generování sestav na PLCs nebo RTUs se spuštěným firmwarem se známými chybami zabezpečení (CVEs).

### <a name="about-this-article"></a>O tomto článku

Tento článek popisuje, jak nastavit pxGrid a Defender pro platformu IoT, aby se zajistilo, že Defender for IoT vloží do Cisco ISE atributy.

### <a name="getting-more-information"></a>Získání dalších informací

Další informace o požadavcích integrace Cisco ISE pxGrid Integration najdete v tématu. <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Požadavky na systém pro integraci

Tento článek popisuje požadavky na systém pro integraci:

Defender pro požadavky IoT

- Defender pro IoT verze 2,5

Požadavky Cisco

- pxGrid verze 2,0

- Cisco ISE verze 2,4

Požadavky sítě

- Ověřte, jestli má zařízení Defender for IoT přístup k rozhraní pro správu Cisco ISE.

- Ověřte, že máte přístup CLI ke všem Defenderům pro zařízení IoT ve vašem podniku.

> [!NOTE]
> S Cisco ISE pxGrid se synchronizují jenom zařízení s adresou MAC.

## <a name="cisco-ise-pxgrid-setup"></a>Nastavení Cisco ISE pxGrid

Tento článek popisuje, jak:

- Nastavení komunikace s pxGrid

- Přihlášení k odběru tématu zařízení koncového bodu

- Generování certifikátů

- Definování nastavení pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Nastavení komunikace s pxGrid

Tento článek popisuje, jak nastavit komunikaci s pxGrid.

Nastavení komunikace:

1. Přihlaste se k Cisco ISE.

1. Vyberte  > **systém** správy a **nasazení**.

1. Vyberte požadovaný uzel. Na kartě Obecné nastavení zaškrtněte **políčko pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Ujistěte se, že je zaškrtnuté políčko pxgrid.":::

1. Vyberte kartu **Konfigurace profilace** .

1. Zaškrtněte **políčko pxGrid**. Přidejte popis.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Snímek obrazovky s popisem přidání":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Přihlášení k odběru tématu zařízení koncového bodu

Ověřte, jestli se uzel ISE pxGrid přihlásil k odběru zařízení koncového bodu. Přejděte na **Správa** >  > **webové klienty** pxGrid Services. Zde můžete ověřit, jestli se ISE přihlásil k odběru zařízení koncového bodu.

## <a name="generate-certificates"></a>Generování certifikátů

Tento článek popisuje, jak vygenerovat certifikáty.

K vygenerování:

1. Vyberte **Správa**  >  **pxGrid Services** a pak vyberte **certifikáty**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Vyberte kartu certifikáty, aby se vygeneroval certifikát.":::

1. V poli **Chci** , vyberte možnost **vygenerovat jeden certifikát (bez žádosti o podepsání certifikátu)**.

1. Vyplňte zbývající pole a vyberte **vytvořit**.

1. Vytvořte klíč certifikátu klienta a certifikát serveru a pak je převeďte do formátu úložiště klíčů Java. Budete je muset zkopírovat do každého Defenderu pro IoT snímač ve vaší síti.

## <a name="define-pxgrid-settings"></a>Definování nastavení pxGrid

Definování nastavení:

1. Vyberte možnost **Správa**  >  **pxGrid Services** a pak vyberte **Nastavení**.

1. Povolte **automatické schvalování nových účtů založených na certifikátech** a povolte **vytváření účtů na základě hesel.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Ujistěte se, že jsou zaškrtnutá obě políčka.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Nastavení programu Defender pro zařízení IoT

Tento článek popisuje, jak nastavit Defender pro zařízení IoT ke komunikaci s pxGrid. Tato konfigurace by se měla provést na všech Defenderech pro zařízení IoT, která budou vkládat data do Cisco ISE.

Nastavení zařízení:

1. Přihlaste se ke senzoru rozhraní CLI.

1. Kopírování `trust.jks` , a, které byly dříve vytvořeny na senzoru. Zkopírujte je do: `/var/cyberx/properties/` .

1. Upravit `/var/cyberx/properties/pxgrid.properties` :

    1. Přidejte klíč a důvěryhodnost a pak uložte názvy souborů a hesla.

    2. Přidejte název hostitele instance pxGrid.

    3. `Enabled=true`

1. Restartujte zařízení.

1. Tento postup opakujte pro každé zařízení ve vaší společnosti, které bude vkládat data.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Zobrazení a Správa detekce v Cisco ISE

1. Detekce koncových bodů se zobrazují na   >  kartě **koncové body** viditelnosti kontextu ISE.

1. Vyberte   >  **profilaci** zásad  >  **Přidat**  >  **pravidla**  >  **+ Podmínka**  >  **vytvořit novou podmínku**.

1. Vyberte **atribut** a pomocí slovníků zařízení IoT Sestavte pravidlo profilace na základě vložených atributů. Následující atributy jsou vloženy.

    - Typ zařízení

    - Revize hardwaru

    - IP adresa

    - Adresa MAC

    - Name

    - ID produktu

    - Protokol

    - Sériové číslo

    - Revize softwaru

    - Dodavatel

Synchronizují se jenom zařízení s adresou MAC.

## <a name="troubleshooting-and-logs"></a>Řešení potíží a protokolování

Protokoly najdete v těchto položkách:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).
