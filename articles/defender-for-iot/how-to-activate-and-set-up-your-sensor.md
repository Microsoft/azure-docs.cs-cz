---
title: Aktivace a nastavení senzoru
description: Tento článek popisuje, jak se přihlásit a aktivovat konzolu senzorů.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7d045ad104f8e2055ae85b74f4aae8f4d3fd61a4
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509515"
---
# <a name="activate-and-set-up-your-sensor"></a>Aktivace a nastavení senzoru

Tento článek popisuje, jak aktivovat senzor a provést počáteční instalaci.

Uživatelé s oprávněním správce můžou aktivovat při prvním přihlášení a v případě potřeby správy aktivace. Instalační program zajistí, aby byl senzor nakonfigurovaný tak, aby optimálně zjišťoval a upozornil.

Analytiky zabezpečení a uživatelé s oprávněním jen pro čtení nemohou senzor aktivovat ani generovat nové heslo.

## <a name="sign-in-and-activation-for-administrator-users"></a>Přihlášení a aktivace pro uživatele správce

Správci, kteří se poprvé přihlásí, by měli ověřit, že mají přístup k aktivačním souborům a souborům pro obnovení hesla staženým během připojování senzorů. Pokud ne, potřebují oprávnění správce zabezpečení Azure, přispěvatele předplatného nebo vlastníka předplatného k vygenerování těchto souborů na portálu Azure Defender pro IoT Portal.

### <a name="first-time-sign-in-and-activation-checklist"></a>Kontrolní seznam pro přihlášení a aktivaci prvního času

Předtím, než se přihlásíte ke konzole senzorů, by měli mít uživatelé správce přístup k:

- IP adresa snímače, která byla definována při instalaci.

- Přihlašovací údaje přihlášeného uživatele pro senzor. Pokud jste pro senzor stáhli ISO, použijte výchozí přihlašovací údaje, které jste dostali při instalaci. Po aktivaci doporučujeme vytvořit nového uživatele *správce* .

- Počáteční heslo. Pokud jste si koupili předem nakonfigurovaný senzor od šipky, musíte při prvním přihlášení vygenerovat heslo.

- Aktivační soubor přidružený k tomuto senzoru. Soubor se vygeneroval a stáhl během připojování senzorů na portálu Defender pro IoT Portal.

- Certifikát podepsaný certifikační autoritou SSL/TLS, který vaše společnost vyžaduje.

### <a name="about-activation-files"></a>O aktivačních souborech

Váš senzor byl připojen k Azure Defenderu pro IoT v určitém režimu správy:

| Typ režimu | Description |
|--|--|
| **Režim připojení ke cloudu** | Informace, které senzor detekuje, se zobrazí v konzole senzorů. Informace o výstrahách se také doručují prostřednictvím služby IoT Hub a dají se sdílet s ostatními službami Azure, jako je Azure Sentinel. |
| **Místně připojený režim** | Informace, které senzor detekuje, se zobrazí v konzole senzorů. Informace o detekci se také sdílí s místní konzolou pro správu, pokud je senzor připojený k němu. |

Pro tento senzor byl během připojování vygenerován a stažen aktivační soubor připojený místně nebo v cloudu. Aktivační soubor obsahuje pokyny pro režim správy snímače. *Do každého snímače, který nasadíte, by se měl nahrát jedinečný aktivační soubor.*  Při prvním přihlášení budete muset odeslat příslušný aktivační soubor pro tento senzor.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender pro IoT Portal, integrovaný senzor.":::

### <a name="about-certificates"></a>Informace o certifikátech

Po instalaci senzoru se vygeneruje místní certifikát podepsaný svým držitelem, který se použije pro přístup ke konzole senzorů. Po prvním přihlášení správce k konzole se k tomuto uživateli zobrazí výzva k připojení certifikátu protokolu SSL/TLS.

K dispozici jsou dvě úrovně zabezpečení:

- Nahrajte certifikát podepsaný certifikační autoritou a vyžádejte si konkrétní požadavky na certifikát a šifrování vyžadované vaší organizací.
- Umožňuje ověření mezi konzolou pro správu a připojenými senzory. Ověření se vyhodnocuje na základě seznamu odvolaných certifikátů a data vypršení platnosti certifikátu. *Pokud se ověření nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření.* Tato možnost je po instalaci povolena ve výchozím nastavení.  

Konzola podporuje následující typy certifikátů:

- Privátní a podniková infrastruktura klíčů (privátní infrastruktura veřejných klíčů)

- Infrastruktura veřejných klíčů (veřejná infrastruktura veřejných klíčů)

- Místně generované na zařízení (místně podepsané svým držitelem) 

  > [!IMPORTANT]
  > Doporučujeme nepoužívat výchozí certifikát podepsaný svým držitelem. Certifikát není zabezpečený a měl by se používat jenom pro testovací prostředí. Vlastníka certifikátu nelze ověřit a zabezpečení systému nelze udržovat. Pro produkční sítě nikdy tuto možnost nepoužívejte.

### <a name="sign-in-and-activate-the-sensor"></a>Přihlaste se a aktivujte senzor.

Přihlášení a aktivace:

1. V prohlížeči pomocí protokolu IP definovaného během instalace přejdete do konzoly senzorů. Otevře se dialogové okno přihlášení.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender pro IoT snímač.":::

1. Zadejte přihlašovací údaje definované během instalace senzoru nebo vyberte možnost **obnovení hesla** . Pokud jste si koupili předem nakonfigurovaný senzor od šipky, vygenerujte nejdřív heslo. Další informace o obnovení hesla najdete v tématu [šetření selhání hesla při počátečním přihlášení](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Po přihlášení se otevře dialogové okno **Aktivace** . Vyberte **nahrát** a přejít na aktivační soubor, který jste stáhli během připojování senzorů.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Vyberte nahrát a přejít do aktivačního souboru.":::

1. Pokud chcete změnit konfiguraci sítě senzoru před aktivací, vyberte odkaz **Konfigurace sítě snímače** . Viz [aktualizace konfigurace sítě senzorů před aktivací](#update-sensor-network-configuration-before-activation).

1. Přijměte podmínky a ujednání.

1. Vyberte **aktivovat**. Otevře se dialogové okno certifikát SSL/TLS.

1. Zadejte název certifikátu.
1. Nahrajte soubory CRT a klíče.
1. Zadejte heslo a v případě potřeby nahrajte soubor PEM.
1. Vyberte **Další**. Otevře se obrazovka ověření. Ve výchozím nastavení je povoleno ověřování mezi konzolou pro správu a připojenými senzory.
1. Vypnutím přepínače **Povolit ověřování na úrovni systému** zakážete ověřování. Doporučujeme povolit ověřování.
1. Vyberte **Uložit**.  

Po nahrání certifikátu podepsaného certifikační autority může být nutné aktualizovat obrazovku.

Informace o nahrání nového certifikátu, podporovaných parametrech certifikátů a práci s příkazy pro certifikáty rozhraní příkazového řádku najdete v tématu [Správa jednotlivých senzorů](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Aktualizovat konfiguraci sítě senzorů před aktivací  

Parametry konfigurace sítě snímače byly definovány během instalace softwaru nebo jste si koupili předkonfigurovaný senzor. Byly definovány následující parametry:

- IP adresa
- DNS
- Výchozí brána
- Maska podsítě
- Název hostitele

Tyto informace možná budete chtít před aktivací senzoru aktualizovat. Například může být nutné změnit předkonfigurované parametry definované šipkou. Před aktivací senzoru můžete také definovat nastavení proxy serveru.

Aktualizace parametrů konfigurace sítě senzoru:

1. Vyberte odkaz **Konfigurace sítě senzoru** v dialogovém okně **Aktivace** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Konfigurace sítě senzorů.":::

2. Zobrazí se parametry definované během instalace. K dispozici je také možnost pro definování serveru proxy. Podle potřeby aktualizujte libovolná nastavení a vyberte **Uložit**.

### <a name="subsequent-sign-ins"></a>Další přihlášení

Po první aktivaci se konzola Azure Defender pro IoT snímač otevře po přihlášení bez vyžadování aktivačního souboru. Potřebujete jenom přihlašovací údaje pro přihlášení.

Po přihlášení se otevře konzola Azure Defender pro IoT.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Konzola Azure Defender pro IoT":::

## <a name="initial-setup-and-learning-for-administrators"></a>Počáteční nastavení a učení (pro správce)

Po prvním přihlášení začne Azure Defender pro IoT snímač monitorovat vaši síť automaticky. Síťová zařízení se zobrazí v sekcích mapa zařízení a inventáře zařízení. Azure Defender pro IoT začne zjišťovat a upozorňovat na všechny bezpečnostní a provozní incidenty, které se ve vaší síti vyskytují. Na základě zjištěných informací pak můžete vytvářet sestavy a dotazy.

Zpočátku se tato aktivita provádí v výukovém režimu, který dává vašemu senzoru informace o běžné činnosti vaší sítě. Senzor například učí zařízení zjištěná ve vaší síti, protokoly zjištěné v síti a přenosy souborů, ke kterým dochází mezi konkrétními zařízeními. Tato aktivita se bude nacházet v rámci aktivity směrného plánu vaší sítě.

### <a name="review-and-update-basic-system-settings"></a>Zkontrolovat a aktualizovat základní systémová nastavení

Zkontrolujte nastavení systému senzoru a ujistěte se, že je senzor nakonfigurovaný tak, aby optimálně zjišťoval a upozornil.

Definujte nastavení systému senzoru. Příklad:

- Definujte ICS (nebo IoT) a oddělené podsítě.

- Definujte aliasy portů pro protokoly specifické pro konkrétní lokalitu.

- Definujte sítě VLAN a názvy, které se používají.

- Pokud se používá DHCP, definujte legitimní rozsahy DHCP.

- Podle potřeby definujte integraci se službou Active Directory a poštovním serverem.

### <a name="disable-learning-mode"></a>Zakázat výukový režim

Po úpravě nastavení systému můžete nechat program Azure Defender for IoT snímač spustit ve výukovém režimu, dokud nebudete přesvědčeni, že detekce systému přesně odráží vaši aktivitu v síti.

Výukový režim by měl běžet přibližně 2 až 6 týdnů v závislosti na velikosti vaší sítě a složitosti. Po zakázání výukového režimu se zobrazí výstraha jakékoli aktivity, která se liší od vaší aktivity podle směrného plánu.

Postup při zakázání výukového režimu:

- Vyberte **nastavení systému** a vypněte možnost **učení** .

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>První přihlášení pro analytiky zabezpečení a uživatele jen pro čtení

Než se přihlásíte, ověřte, že máte následující:

- IP adresa snímače.
- Přihlašovací údaje, které zadal správce.

## <a name="console-tools-overview"></a>Nástroje konzoly: Přehled

Přístup k nástrojům konzoly získáte z vedlejší nabídky.

**Navigace** 

| Okno | Ikona | Description |
| -----------|--|--|
| Řídicí panel | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Zobrazení intuitivního snímku stavu zabezpečení sítě. |
| Mapa zařízení | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Zobrazit síťová zařízení, připojení zařízení a vlastnosti zařízení v mapě. K zobrazení vaší sítě jsou k dispozici různé možnosti přiblížení, zvýraznění a filtrování. |
| Inventář zařízení | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | Inventář zařízení zobrazí seznam atributů zařízení, které tento senzor detekuje. Možnosti jsou k dispozici pro: <br /> – Seřadit nebo filtrovat informace podle polí tabulky a zobrazit zobrazené filtrované informace. <br /> – Exportujte informace do souboru CSV. <br /> – Importujte podrobnosti registru Windows.|
| Výstrahy | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Zobrazit upozornění, když dojde k porušení zásad, se vyskytnou odchylky od chování standardních hodnot nebo se zjistí jakýkoli typ podezřelé aktivity v síti. |
| Sestavy | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Zobrazení sestav založených na dotazech dolování dat. |

**Výsledcích**

| Okno| Ikona | Description |
|---|---|---|
| Časová osa události | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Zobrazit časovou osu pomocí informací o výstrahách, událostech sítě (informativní) a uživatelských operacích, jako jsou přihlášení uživatelů a odstranění uživatelů.|

**Navigace**

| Okno | Ikona | Description |
|---|---|---|
| Dolování dat | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Generování komplexních a podrobných informací o zařízeních vaší sítě v různých vrstvách. |
| Šetření | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Zobrazení trendů a statistik v rozsáhlých různých widgetech. |
| Posouzení rizika | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Zobrazí okno **chyby zabezpečení** . |

**Správce**

| Okno | Ikona | Description |
|---|---|---|
| Uživatelé | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Definujte uživatele a role s různými úrovněmi přístupu. |
| Šíření | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Dodejte informace o výstrahách partnerům a interním zdrojům (například Sentinel), které jsou integrovány s Defenderem pro IoT, e-mailové adresy, servery webhooků a další. <br /> Podrobnosti najdete v podrobnostech [o upozorněních před oznámením](how-to-forward-alert-information-to-partners.md) . |
| Nastavení systému | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Nakonfigurujte nastavení systému. Například definujte nastavení DHCP, zadejte podrobnosti poštovního serveru nebo vytvořte aliasy portů. |
| Nastavení importu | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Zobrazí okno **Nastavení importu** . V informacích o zařízení můžete provádět ruční změny.<br /> Podrobnosti najdete v tématu [Import informací o zařízení](how-to-import-device-information.md) . |

**Podpora**

| Okno| Ikona | Description |
|----|---|---|
| Podpora | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Požádejte o nápovědu [Podpora Microsoftu](https://support.microsoft.com/) . |

### <a name="see-also"></a>Viz také

[Připojení snímače](getting-started.md#4-onboard-a-sensor)

[Spravovat aktivační soubory senzorů](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Určení, který provoz se monitoruje](how-to-control-what-traffic-is-monitored.md)
