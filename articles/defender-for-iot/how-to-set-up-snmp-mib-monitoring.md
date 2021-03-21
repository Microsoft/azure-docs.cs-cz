---
title: Nastavení monitorování SNMP MIB
description: Sledování stavu senzorů můžete provádět pomocí protokolu SNMP. Senzor reaguje na dotazy SNMP odeslané z autorizovaného monitorovacího serveru.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e0ae029323d5b64288c5e61ea28a494c1106a53f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523817"
---
# <a name="set-up-snmp-mib-monitoring"></a>Nastavení monitorování SNMP MIB

Sledování stavu senzorů můžete provádět pomocí protokolu SNMP (Simple Network Management Protocol). Senzor reaguje na dotazy SNMP odeslané z autorizovaného monitorovacího serveru. Monitor SNMP se v pravidelných intervalech znovu dotazuje identifikátory identifikátorů (až 50 krát sekund).

Podporované verze protokolu SNMP jsou SNMP v2 nebo SNMP v3. SNMP používá jako svůj transportní protokol UDP s portem 161 (SNMP).

Než začnete s konfigurací monitorování protokolu SNMP, musíte v bráně firewall otevřít port UDP 161.

## <a name="oids"></a>OID

| Konzola pro správu a senzor | IDENTIFIKÁTOR | Formát | Description |
|--|--|--|--|
| Název zařízení | 1.3.6.1.2.1.1.5.0 | ŘETĚZEC | Název zařízení pro místní konzolu pro správu |
| Dodavatel | 1.3.6.1.2.1.1.4.0 | ŘETĚZEC | Podpora Microsoftu (support.microsoft.com) |
| Platforma | 1.3.6.1.2.1.1.1.0 | ŘETĚZEC | Senzor nebo místní Konzola pro správu |
| Sériové číslo | 1.3.6.1.4.1.9.9.53313.1 | ŘETĚZEC | Řetězec, který používá licence |
| Verze softwaru | 1.3.6.1.4.1.9.9.53313.2 | ŘETĚZEC | Řetězec celé verze Xsense a řetězec pro správu s plnou verzí |
| Využití procesoru | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Indikace na nulu až 100 |
| Teplota procesoru | 1.3.6.1.4.1.9.9.53313.3.2 | ŘETĚZEC | Indikace Celsia na nulu až 100 na vstupu ze systému Linux |
| Využití paměti | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Indikace na nulu až 100 |
| Využití disku | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Indikace na nulu až 100 |
| Stav služby | 1.3.6.1.4.1.9.9.53313.5 | ŘETĚZEC | Online nebo offline v případě, že jedna ze čtyř důležitých součástí je mimo provoz |
| Šířka pásma | Mimo rozsah pro 2,4 |  | Šířka pásma přijatá na každém monitorovaném rozhraní v Xsense |

   - Neexistující klíče reagují s hodnotou null, HTTP 200 na základě [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - U všech architektur a fyzických senzorů by se měly testovat MIB související s hardwarem (využití procesoru, teplota procesoru, využití paměti, využití disku). Očekává se, že teplota procesoru u virtuálních počítačů nebude platná.

Můžete si stáhnout protokol, který obsahuje všechny dotazy SNMP, které senzor obdrží, včetně dat připojení a nezpracovaných dat z paketu.

Definování sledování stavu protokolu SNMP v2:

1. V postranní nabídce vyberte **nastavení systému**.

2. V podokně **aktivní zjišťování** vyberte možnost **monitorování MIB protokolu SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Upravte okno SNMP.":::

3. V části **povolené hostitele** vyberte **Přidat hostitele** a zadejte IP adresu serveru, který provádí monitorování stavu systému.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Zadejte IP adresu pro povolené hostitele.":::

4. V části **ověřování** zadejte do pole **řetězec komunity SNMP v2** řetězec. Řetězec komunity SNMP může obsahovat až 32 znaků a obsahovat libovolnou kombinaci alfanumerických znaků (velká písmena, malá písmena a čísla). Mezery nejsou povolené.

5. Vyberte **Uložit**.

Postup při definování sledování stavu protokolu SNMP v3:

1. V postranní nabídce vyberte **nastavení systému**.

2. V podokně **aktivní zjišťování** vyberte možnost **monitorování MIB protokolu SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Upravte okno SNMP.":::

3. V části **povolené hostitele** vyberte **Přidat hostitele** a zadejte IP adresu serveru, který provádí monitorování stavu systému.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Zadejte IP adresu pro povolené hostitele.":::

4. V části **ověřování** nastavte následující parametry:

    | Parametr | Popis |
    |--|--|
    | **Uživatelské jméno** | Uživatelské jméno SNMP může obsahovat až 32 znaků a obsahovat libovolnou kombinaci alfanumerických znaků (velká písmena, malá písmena a číslice). Mezery nejsou povolené. <br /> <br />Uživatelské jméno pro ověřování SNMP v3 musí být nakonfigurované v systému a na serveru SNMP. |
    | **Heslo** | Zadejte heslo pro ověřování citlivé na velká a malá písmena. Heslo pro ověřování může obsahovat 8 až 12 znaků a obsahovat libovolnou kombinaci alfanumerických znaků (velká písmena, malá písmena a číslice). <br /> <br/>Uživatelské jméno pro ověřování SNMP v3 musí být nakonfigurované v systému a na serveru SNMP. |
    | **Typ ověření** | Vyberte MD5 nebo SHA. |
    | **Šifrování** | Vyberte DES nebo AES. |
    | **Tajný klíč** | Klíč musí obsahovat přesně osm znaků a obsahovat libovolnou kombinaci alfanumerických znaků (velká písmena, malá písmena a číslice). |

5. Vyberte **Uložit**.

## <a name="see-also"></a>Viz také

[Exportovat protokoly řešení potíží](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
