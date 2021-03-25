---
title: Příručka pro správce Azure IoT Central
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled role správce v IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110487"
---
# <a name="iot-central-administrator-guide"></a>Příručka pro správce IoT Central

*Tento článek se týká správců.*

IoT Central aplikace umožňuje monitorovat a spravovat miliony zařízení během jejich životního cyklu. Tato příručka je určena správcům, kteří spravují aplikace IoT Central.

V IoT Central správce:

- Spravuje uživatele a role v aplikaci.
- Spravuje zabezpečení, jako je ověřování zařízení.
- Nakonfiguruje nastavení aplikace.
- Upgraduje aplikace.
- Exportuje a sdílí aplikace.
- Monitoruje stav aplikace.

## <a name="users-and-roles"></a>Uživatelé a role

IoT Central používá systém řízení přístupu založený na rolích ke správě uživatelských oprávnění v rámci aplikace. IoT Central má tři předdefinované role pro správce, sestavovatele řešení a operátory. Správce může vytvořit vlastní role s konkrétními sadami oprávnění. Správce zodpovídá za přidání uživatelů do aplikace a jejich přiřazení k rolím.

Další informace najdete v tématu [Správa uživatelů a rolí v aplikaci IoT Central](howto-manage-users-roles.md).

## <a name="application-security"></a>Zabezpečení aplikací

Zařízení, která se připojují k vaší IoT Central aplikaci, typicky jako přihlašovací údaje používají certifikáty X. 509 nebo sdílené přístupové podpisy (SAS). Správce spravuje certifikáty nebo klíče skupiny, z nichž jsou odvozena pověření zařízení.

Další informace najdete v tématu [registrace skupiny X. 509](concepts-get-connected.md#x509-group-enrollment), [registrace skupiny SAS](concepts-get-connected.md#sas-group-enrollment)a [Postup při navádění certifikátů zařízení x. 509](how-to-roll-x509-certificates.md).

Správce může také vytvořit a spravovat tokeny rozhraní API, které klientská aplikace používá k ověření pomocí aplikace IoT Central. Klientské aplikace používají REST API k interakci s IoT Central.

## <a name="configure-an-application"></a>Konfigurace aplikace

Správce může nakonfigurovat chování a vzhled aplikace IoT Central. Další informace najdete v následujících tématech:

- [Změnit název a adresu URL aplikace](howto-administer.md#change-application-name-and-url)
- [Přizpůsobení uživatelského rozhraní](howto-customize-ui.md)
- [Přesunutí aplikace na jiné cenové tarify](howto-view-bill.md)
- [Konfigurace nahrávání souborů](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Export aplikace

Správce může:

- Pokud potřebujete pouze duplicitní kopii aplikace, vytvořte kopii aplikace. Například je možné, že budete potřebovat duplicitní kopii pro testování.
- Pokud plánujete vytvořit více kopií, vytvořte šablonu aplikace z existující aplikace.

Další informace najdete v tématu [Export aplikace Azure IoT](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrovat na novou verzi

Správce může migrovat aplikaci na novější verzi. Nově vytvořená aplikace je aktuálně aplikace v3. Správce může potřebovat migrovat aplikaci v2 do aplikace v3.

Další informace najdete v tématu [migrace aplikace IoT Central v2 na V3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorování stavu aplikace

Správce může použít IoT Central metriky k vyhodnocení stavu připojených zařízení a stavu spouštění exportu dat.

Pokud chcete zobrazit metriky, může správce použít grafy v dotazech Azure Portal, REST API, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Další informace najdete v tématu [monitorování celkového stavu aplikace IoT Central](howto-monitor-application-health.md).

## <a name="tools"></a>nástroje

Mnohé z nástrojů, které používáte jako správce, jsou k dispozici v části **Správa** každé aplikace IoT Central. K dokončení některých úloh správy můžete také použít následující nástroje:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat vaši aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [správě uživatelů a rolí](howto-manage-users-roles.md) v Azure IoT Central.
