---
title: Osvědčené postupy pro vývoj zařízení v Azure IoT Central | Microsoft Docs
description: Tento článek popisuje osvědčené postupy pro připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054563"
---
# <a name="best-practices-for-device-development"></a>Osvědčené postupy pro vývoj zařízení

*Tento článek se týká vývojářů zařízení.*

Tato doporučení ukazují, jak implementovat zařízení, která využívají integrované zotavení po havárii a automatické škálování v IoT Central.

Následující seznam zobrazuje tok vysoké úrovně, když se zařízení připojuje k IoT Central:

1. Pomocí DPS zřiďte zařízení a získejte připojovací řetězec zařízení.

1. Pomocí připojovacího řetězce se připojte k internímu koncovému bodu IoT Hub IoT Central. Posílání dat a příjem dat z vaší aplikace IoT Central.

1. Pokud se zařízení stane selháním připojení, pak v závislosti na typu chyby zkuste připojení znovu nebo zařízení znovu zajistěte.

## <a name="use-dps-to-provision-the-device"></a>Zřízení zařízení pomocí DPS

Pokud chcete zřídit zařízení s DPS, použijte ID oboru, přihlašovací údaje a ID zařízení z vaší aplikace IoT Central. Další informace o typech přihlašovacích údajů najdete v tématu Registrace [skupiny X. 509](concepts-get-connected.md#x509-group-enrollment) a [registrace skupiny SAS](concepts-get-connected.md#sas-group-enrollment). Další informace o ID zařízení najdete v tématu [registrace zařízení](concepts-get-connected.md#device-registration).

Po úspěšném obnovení DPS vrátí připojovací řetězec, který může zařízení použít k připojení k vaší IoT Central aplikaci. Informace o řešení chyb zřizování najdete v tématu [Zkontrolujte stav zřizování vašeho zařízení](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

Zařízení může připojovací řetězec Uložit do mezipaměti, aby ho bylo možné použít pro pozdější připojení. Zařízení se ale musí připravit na [zpracování selhání připojení](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Připojení k IoT Central

Pomocí připojovacího řetězce se připojte k internímu koncovému bodu IoT Hub IoT Central. Připojení umožňuje odeslat telemetrii do aplikace IoT Central, synchronizovat hodnoty vlastností s vaší aplikací IoT Central a reagovat na příkazy odesílané vaší IoT Central aplikaci.

## <a name="handle-connection-failures"></a>Zpracování chyb připojení

Pro účely škálování nebo zotavení po havárii může IoT Central aktualizovat svůj základní IoT Hub. Aby se zachovalo připojení, váš kód zařízení by měl zpracovávat konkrétní chyby připojení navázáním připojení k novému koncovému bodu IoT Hub.

Pokud zařízení získá při připojení některou z následujících chyb, měli byste znovu provést krok zřizování s DPS a získat nový připojovací řetězec. Tyto chyby znamenají, že připojovací řetězec, který zařízení používá, již není platný:

- Nedosažitelný IoT Hub koncový bod.
- Token zabezpečení s vypršenou platností
- Zařízení je v IoT Hub zakázané.

Pokud zařízení získá při připojení některou z následujících chyb, měla by k opakovanému připojení použít záložní strategii. Tyto chyby znamenají, že připojovací řetězec, který zařízení používá, je stále platný, ale přechodné podmínky zastavuje připojení zařízení:

- Obsluha zařízení se zablokovala.
- Došlo k vnitřní chybě 500 od služby.

Další informace o kódech chyb zařízení najdete v tématu [řešení potíží s připojením zařízení](troubleshoot-connection.md).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si ukázkový kód, který ukazuje, jak používat tokeny SAS v [kurzu: vytvoření a připojení klientské aplikace do aplikace Azure IoT Central.](tutorial-connect-device.md)
- Naučte se, jak [připojit zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikaci](how-to-connect-devices-x509.md) .
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
- Zjistěte, jak [v aplikaci IoT Central Azure definovat nový typ zařízení IoT](./howto-set-up-template.md) .
- Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md)
