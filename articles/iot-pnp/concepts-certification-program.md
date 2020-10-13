---
title: Program zařízení s certifikací Azure | Microsoft Docs
description: Seznamte se s programem zařízení s certifikací Azure.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9580f1c7a29b19bb926a7079b95cb3ed0b314d05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579771"
---
# <a name="what-is-the-azure-certified-device-program"></a>Co je program zařízení s certifikací Azure?

Program zařízení s certifikací Azure zajišťuje bezproblémovou spolupráci řešení zákazníků se službami Azure. Program používá nástroje, služby a tržiště ke sdílení znalostí a osvědčených postupů s komunitou tvůrců zařízení a řešení.

Tento program je navržený tak, aby:

- **Poskytněte zákazníkům důvěru:** Zákazníci si můžou bezdůvodně koupit zařízení, která Microsoft certifikována pro použití se službami Azure.

- **Pomáhat zákazníkům najít správná zařízení pro své řešení:** Tvůrci zařízení můžou v rámci procesu certifikace publikovat jedinečné schopnosti svých zařízení. Zákazníci můžou snadno vyhledat produkty, které vyhovují jejich potřebám.

- **Zvýšit úroveň certifikovaných zařízení:** Tvůrci zařízení získají zvýšenou viditelnost, kontaktuje se zákazníky a používání značky zařízení Certified v Azure.

Tento článek popisuje, jak:

- Připojte svoji společnost do programu zařízení s certifikací Azure.
- Určete, která certifikace se má použít pro vaše zařízení.
- Vyhledejte požadavky programu a další materiály, které vám pomohou začít s testováním.
- K ověření zařízení použijte portál zařízení s certifikací Azure.

## <a name="onboarding"></a>Onboarding

Pokud chcete zařízení certifikovat na [portálu zařízení s certifikací Azure](https://aka.ms/acdp), musíte provést následující kroky:

1. Ujistěte se, že vaše společnost má účet Azure Active Directory s použitím pracovního nebo školního tenanta.
2. Připojte se k [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) pomocí svého účtu.
3. Po připojení k programu MPN se přihlaste k [portálu zařízení s certifikací Azure](https://aka.ms/acdp) .
4. Kontrola a podepsání [smlouvy programu](https://aka.ms/acdagreement) na stránce profilu společnosti

### <a name="company-profile"></a>Profil společnosti

Pokud chcete spravovat profil vaší společnosti na portálu zařízení s certifikací Azure, vyberte **Profil společnosti**. Profil společnosti zahrnuje adresu URL, e-mailovou adresu a logo společnosti. Než budete pokračovat v operacích certifikace, přijměte na této stránce smlouvu o programu.

Stránka Popis zařízení v katalogu zařízení s certifikací Azure používá informace o profilu společnosti.

## <a name="choose-the-certification"></a>Zvolit certifikaci

Existují tři různé certifikace, z nichž každá se zaměřuje na poskytování různých zákaznických hodnot. V závislosti na typu zařízení, které vytváříte, a cílové cílové skupině můžete zvolit, jaké certifikace nebo certifikace se mají použít:

### <a name="azure-certified-device"></a>Zařízení s certifikací Azure

_Certifikace zařízení_ s certifikací Azure ověří, že se může zařízení připojit k Azure IoT Hub a bezpečně zřídit prostřednictvím služby Device Provisioning (DPS). Tato certifikace odráží funkce a interoperabilitu zařízení, které jsou nezbytné pro pokročilejší certifikace.

- Další informace najdete v tématu [požadavky na certifikaci](https://aka.ms/acdrequirements).
- Další informace o použití DPS k připojení zařízení k Azure IoT Hub najdete v tématu [Přehled zřízení zařízení](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>IoT Plug and Play

_Certifikace IoT technologie Plug and Play_, přírůstkovou certifikaci pro certifikaci zařízení certifikovaných v Azure, zjednodušuje proces vytváření zařízení bez vlastního kódu zařízení. IoT technologie Plug and Play umožňuje hardwarovým partnerům vytvářet zařízení, která se dají snadno integrovat s cloudovým řešením založenými na Azure IoT Central a řešeních třetích stran.

- Další informace najdete v tématu [požadavky na certifikaci](https://aka.ms/acdiotpnprequirements).
- Další informace o tom, jak připravit zařízení pro IoT technologie Plug and Play testování, najdete v tématu [jak certifikovat technologie Plug and Play zařízení IoT](howto-certify-device.md).

### <a name="edge-managed"></a>Spravovaná hrana

_Hraniční spravovaná certifikace_, přírůstková certifikace pro certifikaci zařízení s certifikací Azure, se zaměřuje na standardy správy zařízení pro zařízení Azure IoT se systémem Windows, Linux nebo RTO. V současné době se certifikace tohoto programu zaměřuje na kontrolu kompatibility modulu runtime Edge pro nasazení a správu modulů.

> [!TIP]
> Tento program byl dříve označován jako _IoT Edge certifikační program_.

- Další informace najdete v tématu [požadavky na certifikaci](https://aka.ms/acdedgemanagedrequirements).
- Další informace o IoT Edge najdete v tématu [IoT Edge Overview](../iot-edge/about-iot-edge.md).
- Další informace o podporovaných operačních systémech a kontejnerech najdete v tématu [IoT Edge podporovaných systémech](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Použití portálu zařízení s certifikací Azure

Tato část shrnuje, jak používat [portál zařízení s certifikací Azure](https://certify.azure.com). Další informace najdete v [příručce Začínáme s portálem](https://aka.ms/acdhelp).

Chcete-li certifikovat zařízení v programu zařízení s certifikací Azure, proveďte následující čtyři kroky:

1. Zadejte podrobnosti o zařízení.
2. Testování zařízení
3. Odeslat a dokončit kontrolu
4. Publikování do katalogu zařízení s certifikací Azure (volitelné)

### <a name="provide-device-details"></a>Zadání podrobností o zařízení

Pro každé zařízení, které chcete certifikovat, použijte formuláře na portálu pro certifikace k záznamu podrobností o hardwaru zařízení, pokynech k instalaci a marketingovém materiálu:

- **Informace o zařízení:** Shromažďuje informace o zařízení, jako je název, popis, podrobnosti o hardwaru a operační systém.
- **Příručka Začínáme**: dokument PDF, který zákazník může použít k rychlému používání vašeho produktu. K dispozici jsou [ukázkové šablony](https://aka.ms/GSTemplate) .
- **Podrobnosti o marketingu:** Poskytněte zákaznickou informace připravené pro vaše zařízení, například informace o obrázku a distributorovi.
- **Další oborové certifikace:** Volitelný oddíl, který vám umožní zadat informace o všech dalších certifikačních autoritách, které zařízení má.

### <a name="test-the-device"></a>Testování zařízení

Tato fáze komunikuje s vaším zařízením a spustí sérii testů, které po zařízení používá DPS pro připojení k IoT Hub. Po dokončení můžete zobrazit sadu souborů protokolu s výsledky testu zařízení.

Certifikační portál obsahuje pokyny, jak se připojit k instanci IoT Hub, která se používá pro testování. Připojení DPS můžete vytvořit prostřednictvím kterékoli z [podporovaných metod ověřování](https://aka.ms/acdAttestation).

Tým zařízení s certifikací Azure se může spojit s tvůrcem zařízení a získat další ruční ověření zařízení.

### <a name="submit-and-publish"></a>Odeslat a publikovat

Poslední požadovaná fáze je odeslat projekt ke kontrole. Tento krok oznamuje členovi týmu zařízení Certified v Azure, aby zkontroloval úplnost projektu, včetně podrobností o zařízení a marketingu a úvodní příručce. Člen týmu vás může kontaktovat na e-mailové adrese společnosti, která byla dříve k dispozici s dotazy nebo upravit žádosti před schválením.

Pokud vaše zařízení vyžaduje další ruční ověření v rámci certifikace, obdržíte v tomto okamžiku oznámení.

Když je zařízení certifikováno, můžete se rozhodnout publikovat podrobnosti o produktu v katalogu zařízení s certifikací Azure pomocí funkce **publikovat do katalogu** na stránce souhrnu produktu.

## <a name="if-you-have-questions"></a>Pokud máte dotazy

[iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)Pokud máte nějaké dotazy týkající se certifikačních programů, certifikačního portálu nebo katalogu zařízení s certifikací Azure, obraťte se na tým.

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o programu zařízení s certifikací Azure, je navržený další krok, kde se dozvíte, [jak certifikovat technologie Plug and Play zařízení IoT](howto-certify-device.md).
