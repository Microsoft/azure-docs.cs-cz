---
title: O integraci Forescout
description: Integrace Azure Defenderu pro IoT s platformou Forescout poskytuje novou úroveň centralizované viditelnosti, monitorování a řízení pro IoT a na šířku.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784063"
---
# <a name="about-the-forescout-integration"></a>O integraci Forescout

Azure Defender pro IoT přináší platformě ICS a IoT kyberbezpečnosti, kterou vytvořili odborníci z modrého týmu, a sleduje záznam o tom, jak se chránit kritická národní infrastruktura. Defender for IoT je jediná platforma s patentovou analýzou ochrany před internetovými útoky a strojovým učením. Defender pro IoT poskytuje:

- Okamžité přehledy o ICS zařízení na šířku s rozsáhlým rozsahem podrobností o atributech.
- Bohaté informace o protokolech, zařízeních, aplikacích a jejich chování s obsáhlou integrovanou službou ICS
- Okamžité přehledy o ohrožení zabezpečení a známých a nulových hrozeb.
- Automatizovaná technologie ICS pro vytváření hrozeb pro předpověď nejpravděpodobnějších cest k cíleným útokům ICS prostřednictvím proprietárních analýz.

Integrace programu Defender for IoT s platformou Forescout poskytuje novou úroveň centralizované viditelnosti, monitorování a řízení pro IoT a na šířku.

Tyto přemostění platforem umožňují automatizované zobrazení a správu zařízení do dříve nedosažitelných zařízení ICS a v případě sila pracovních postupů.

Integrace poskytuje analytikům SOC s víceúrovňovým přehledem o protokolech nasazených v průmyslových prostředích. Podrobnosti o položkách, jako jsou firmware, typy zařízení, operační systémy a hodnocení rizik, jsou k dispozici na základě vlastního programu Azure Defender pro technologie IoT.

> [!Note]
> Odkazy na CyberX odkazují na Azure Defender pro IoT.
## <a name="devices"></a>Zařízení

### <a name="device-visibility-and-management"></a>Viditelnost a Správa zařízení

Inventář zařízení je obohacen o kritické atributy zjištěné v programu Defender pro platformu IoT. Tím zajistíte, že:

- Získejte ucelenou a nepřetržitou viditelnost zařízení z jednoho podokna, na šířku.
- Získejte informace o rizicích za v reálném čase.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventář zařízení":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Podrobnosti o zařízení":::

### <a name="device-control"></a>Řízení zařízení

Integrace Forescout pomáhá zkrátit dobu potřebnou k tomu, aby organizace v oblasti průmyslu a kritické infrastruktury rozpoznala, prošetřila a pracovala s internetovými hrozbami.

- Pomocí Azure Defenderu pro IoT a zařízení můžete cyklům zabezpečení zavřít a aktivovat akce zásad Forescout. Můžete třeba automaticky posílat e-maily s výstrahami SOC správcům, když se zjistí konkrétní protokoly nebo když se změní podrobnosti firmwaru.

- Korelovat Defender pro informace IoT s dalšími *Forescout moduly eyeExtended* , které dohlíží na monitorování, správu incidentů a řízení zařízení.

## <a name="system-requirements"></a>Požadavky na systém

- Azure Defender pro IoT verze 2,4 nebo vyšší
- Forescout verze 8,0 nebo vyšší
- Licence pro modul *EyeExtend Forescout* pro Azure Defender pro IoT Platform.

### <a name="getting-more-forescout-information"></a>Získání dalších informací Forescout

Další informace o platformě Forescout najdete v [Centru zdrojů Forescout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Nastavení systému

Tento článek popisuje, jak nastavit komunikaci mezi Defenderem pro platformu IoT a platformou Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Nastavení programu Defender for IoT Platform

Aby bylo zajištěno, že komunikace z programu Defender pro IoT do Forescout vygeneruje přístupový token v programu Defender pro IoT.

Přístupové tokeny umožňují externím systémům přístup k datům zjištěným pomocí programu Defender pro IoT a provádění akcí s těmito daty pomocí externích REST API, přes připojení SSL. Přístup k programu Azure Defender pro IoT REST API můžete vygenerovat tak, že vygenerujete přístupové tokeny.

Vygenerování tokenu:

1. Přihlaste se k programu Defender for IoT snímač, na který se dotazuje Forescout.

1. Vyberte **nastavení systému** a potom v části **Obecné** vyberte **přístupové tokeny** . Otevře se dialogové okno **přístupové tokeny** .
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Přístupové tokeny":::
1. V dialogovém okně **přístupové tokeny** vyberte **generovat nový token** .
1. Zadejte popis tokenu v dialogovém okně **nový přístupový token** .
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nový přístupový token":::
1. Vyberte **Další**. Token se zobrazí v dialogovém okně. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Zobrazit token":::
   > [!NOTE]
   > *Zaznamenejte token na bezpečném místě. Budete ho potřebovat při konfiguraci platformy Forescout*.
1. Vyberte **Dokončit**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Dokončení přidávání tokenu":::

### <a name="set-up-the-forescout-platform"></a>Nastavení platformy Forescout

Můžete nastavit platformu Forescout ke komunikaci s Defenderem pro IoT snímač.

Nastavení:

1. Nainstalujte *modul Forescout eyeExtend pro CyberX* na platformě Forescout.

1. Přihlaste se ke konzole pro vyvážení a v nabídce **nástroje** vyberte **možnost možnosti** . Otevře se dialogové okno **Možnosti** .

1. Přejděte na složku **moduly** a vyberte ji.

1. V podokně **moduly** vyberte **platformu CyberX**. Otevře se okno Defender for IoT Platform.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Nastavení modulu Azure Defender pro IoT":::

   Zadejte následující informace:

   - **Adresa serveru** – zadejte IP adresu programu Defender for IoT snímač, na kterou se bude dotazovat zařízení Forescout.
   - **Přístupový token** – zadejte přístupový token generovaný pro program Defender pro IoT snímač, který se připojí k zařízení Forescout. Pokud chcete vygenerovat token, přečtěte si téma [Nastavení programu Defender pro platformu IoT](#set-up-the-defender-for-iot-platform).

1. Vyberte **Použít**.

Pokud chcete, aby platforma Forescout komunikovala s jiným senzorem:

1. Vytvořte nový přístupový token v příslušném programu Defender pro IoT snímač.

1. V dialogovém okně **Forescout moduly**  >  **CyberX Platform** :

   - Odstraňte zobrazené informace.
   
   - Zadejte novou IP adresu snímače a informace o novém přístupovém tokenu.

### <a name="verify-communication"></a>Ověřit komunikaci

Po nakonfigurování programu Defender pro IoT a Forescout otevřete dialogové okno přístupové tokeny snímače v programu Defender pro IoT.

Pokud se v poli **použito** pro tento token zobrazuje není **N/a** , připojení mezi senzorem a zařízením Forescout nefunguje.

**Používá** se k poslednímu přijetí externího volání s tímto tokenem.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Ověřuje, že se token přijal.":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Zobrazení Defenderu pro zjišťování IoT v Forescout

Zobrazení atributů zařízení:

1. Přihlaste se k platformě Forescout a pak přejděte k **inventáři prostředků**.

1. Přejděte na **platformu CyberX**. Pro zařízení zjištěná v programu Defender pro IoT se zobrazí následující atributy zařízení.

   | Položka | Popis |
   |--|--|
   | Autorizováno službou Azure Defender pro IoT | Zařízení zjištěné v síti programem Defender pro IoT během období síťového učení. |
   | Firmware | Podrobnosti o firmwaru zařízení. Například model a podrobnosti o verzi. |
   | Name | Název zařízení |
   | Operační systém | Operační systém zařízení. |
   | Typ | Typ zařízení. Například PLC, historian nebo Engineering Station. |
   | Dodavatel | Dodavatel zařízení. Například automatizace služby Rockwell. |
   | Úroveň rizika | Úroveň rizika vypočtená Defenderem pro IoT |
   | Protokoly | Protokoly zjištěné v provozu vygenerovaném zařízením. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Zobrazit atributy firmwaru.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Zobrazení atributů dodavatelů.":::

### <a name="viewing-more-details"></a>Zobrazení dalších podrobností

Zobrazit dodatečné informace o zařízení, které jsou směrovány přes Defender pro IoT. Například informace o kompatibilitě a zásadách Forescout.

Pokud to chcete provést, klikněte pravým tlačítkem na zařízení v části **hostitelé inventáře zařízení** . Zobrazí se dialogové okno Podrobnosti o hostiteli s dalšími informacemi.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Podrobnosti o hostiteli":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Vytvoření zásad Azure Defenderu pro IoT v Forescout

Zásady Forescout se dají použít k automatizaci řízení a správy zařízení zjištěných v programu Defender pro IoT. Třeba

- Když se zjistí konkrétní verze firmwaru, automaticky e-mailem SOC správcům.

- Přidejte do skupiny Forescout konkrétní program Defender pro zařízení IoT, který vám umožní další zpracování v pracovních postupech incidentu a zabezpečení, například s dalšími SIEM integrací.

Vytvoření vlastní zásady Forescout s využitím vlastností podmínky v programu Defender pro IoT

Přístup k programu Defender pro vlastnosti IoT:

1. V dialogovém okně **podmínky zásad** přejděte do **stromu vlastností** .

1. Rozbalte složku **platformy CyberX** ve **stromu vlastností**. K dispozici jsou následující vlastnosti Defender pro IoT.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Vlastnosti":::

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).
