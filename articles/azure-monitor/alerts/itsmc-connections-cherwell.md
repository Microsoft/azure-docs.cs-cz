---
title: Připojení Cherwell ke konektoru pro správu služeb IT
description: Tento článek poskytuje informace o tom, jak Cherwell pomocí konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 0f488b047cf1323619d9603204877a55b413c295
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802467"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Připojení Cherwell ke konektoru pro správu služeb IT

Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi instancí Cherwell a konektorem Service Management Connector (ITSMC) v Log Analytics pro centrální správu vašich pracovních položek.

> [!NOTE]
> Od 1. října 2020 Cherwell ITSM Integration s Azure Alert už nebude pro nové zákazníky povolený. Nová připojení ITSM se nepodporují.
> Existující připojení ITSM budou podporována.

Následující části obsahují podrobné informace o tom, jak připojit produkt Cherwell k ITSMC v Azure.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).
- ID klienta bylo vygenerováno. Další informace: [vygenerujte ID klienta pro Cherwell](#generate-client-id-for-cherwell).
- Role uživatele: správce.

## <a name="connection-procedure"></a>Postup připojení

K vytvoření připojení Cherwell použijte následující postup:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2. V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.
    ![Nové připojení](/azure/azure-monitor/alerts/media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance Cherwell, ke které se chcete připojit ITSMC.  Tento název použijete později při konfiguraci pracovních položek v tomto ITSM/zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **Cherwell.** |
| **Uživatelské jméno**   | Zadejte uživatelské jméno Cherwell, které se může připojit k ITSMC. |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo slouží pouze k vytváření tokenů ověřování a nejsou uloženy kdekoli v rámci služby ITSMC.|
| **Adresa URL serveru**   | Zadejte adresu URL instance Cherwell, ke které se chcete připojit ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověřování tohoto připojení, které jste vygenerovali v instanci Cherwell.   |
| **Rozsah synchronizace dat**   | Vyberte Cherwell pracovní položky, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky se importují do Log Analytics.   **Možnosti:**  Incidenty, žádosti o změnu. |
| **Synchronizovat data** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM v případě neexistujícího systému služby CI ovlivněné položky konfigurace. **Výchozí**: zakázáno. |

![Připojení Cherwell](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance Cherwell se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci Cherwell.

Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Vygenerovat ID klienta pro Cherwell

K vygenerování ID klienta/klíče pro Cherwell použijte následující postup:

1. Přihlaste se ke své instanci Cherwell jako správce.
2. Klikněte na **zabezpečení**  >  **Upravit REST API nastavení klienta**.
3. Vyberte **vytvořit nový**  >  **tajný klíč** klienta klienta.

    ![ID uživatele Cherwell](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Další kroky

* [Přehled ITSM konektoru](itsmc-overview.md)
* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)
