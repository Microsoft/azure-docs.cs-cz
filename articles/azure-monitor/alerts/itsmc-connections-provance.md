---
title: Připojit prokázání pomocí konektoru pro správu služeb IT
description: Tento článek poskytuje informace o tom, jak prokázat pomocí konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 542b127823a73058f319e6a39c001bd563f042ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045373"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Připojit prokázání pomocí konektoru pro správu služeb IT

Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi vaší instancí nastavování a konektorem IT Service Management Connector (ITSMC) v Log Analytics centrálně spravovat pracovní položky.

> [!NOTE]
> Od 1. října-2020 inITSMování pro instalování s upozorněním na Azure už nebude pro nové zákazníky povolené. Nová připojení ITSM se nepodporují.
> Existující připojení ITSM budou podporována.

Následující části obsahují podrobné informace o tom, jak připojit produkt pro prokázání do ITSMC v Azure.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).
- Aplikace pro zakazování by měla být zaregistrovaná ve službě Azure AD – a ID klienta je dostupné. Podrobné informace najdete v tématu [Konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Role uživatele: správce.

## <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu vytvořte připojení k prokázání:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2. V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.
    ![Nové připojení](media/itsmc-overview/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance pro prov, ke kterému se chcete připojit pomocí ITSMC.  Tento název použijete později při konfiguraci pracovních položek v tomto ITSM/zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **prov**. |
| **Uživatelské jméno**   | Zadejte uživatelské jméno, které se může připojit k ITSMC.    |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo slouží pouze k vytváření tokenů ověřování a nejsou uloženy kdekoli v rámci služby ITSMC.|
| **Adresa URL serveru**   | Zadejte adresu URL vaší instance prov, ke které se chcete připojit ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověřování tohoto připojení, které jste vygenerovali v instanci prokázání.  Další informace o ID klienta najdete v tématu [Konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Rozsah synchronizace dat**   | Pomocí ITSMC vyberte pracovní položky prov, které chcete synchronizovat s Azure Log Analytics.  Tyto pracovní položky se importují do Log Analytics.   **Možnosti:**   Incidenty, žádosti o změnu.|
| **Synchronizovat data** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM v případě neexistujícího systému služby CI ovlivněné položky konfigurace. **Výchozí**: zakázáno.|

![Snímek obrazovky, který zvýrazní název připojení a seznam partnerských typů.](media/itsmc-connections-provance/itsm-connections-provance-latest.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance prov se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci prov.

## <a name="next-steps"></a>Další kroky

* [Přehled ITSM konektoru](itsmc-overview.md)
* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)