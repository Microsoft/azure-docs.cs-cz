---
title: Moduly Azure IoT Edge
description: IoT Edge modulu nabízet na webu Azure Marketplace pro vydavatele aplikace a služby.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: 46740e925a5d4e94c314105aba4920caf01532e7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985604"
---
# <a name="iot-edge-modules"></a>Moduly IoT Edge

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platformy je založená na cloudu Azure.  Tato platforma umožňuje uživatelům nasadit cloudové úlohy spouštět přímo na zařízeních IoT.  Modul IoT Edge můžete spustit v režimu offline úloh a provádět analýzu dat místně. Tento typ nabídky pomáhá šetřit šířku pásma, chránit místní a citlivá data a nabízí doba odezvy s nízkou latencí.  Nyní máte možnost využít výhod těchto předdefinovaných úloh. Až dosud nebyly k dispozici pouze na několik vlastní řešení od Microsoftu.  Jste museli investovat čas a prostředky na vytváření vlastních řešení IoT.

Zavedením [moduly IoT Edge na webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), nyní je k dispozici na jednom místě, kde vydavatelé můžou zveřejnit a prodej svých řešení IoT cílové skupině. Vývojáři IoT nakonec můžete najít a zakoupit funkce pro zrychlení vývoje svých řešení.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Klíčové výhody modulů IoT Edge v Azure Marketplace:

| **Pro vydavatele**    | **Pro zákazníky (vývojáře IoT)**  |
| :------------------- | :-------------------|
| Oslovení miliónů vývojářů chtějí vytvářet a nasazovat řešení IoT Edge.  | Vytvoření IoT Edge řešení bez obav použití komponent v zabezpečené a otestovaná. |
| Po publikování a poběží na veškerý hardware IoT Edge, která podporuje kontejnery. | Zkracujte dobu uvedení na trh díky nalezení a nasazení 1. a 3. stran moduly IoT Edge pro konkrétní potřeby. |
| Finanční zhodnocení pomocí flexibilních možností fakturace <ul> <li> U bezplatných služeb a používání vlastní licence (BYOL). </li> </ul> | Nákupy s modelem fakturace vázaným podle vaší volby. <ul> <li> U bezplatných služeb a používání vlastní licence (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co je modul IoT Edge?

Azure IoT Edge umožňuje nasazovat a spravovat obchodní logiku na hraničních zařízeních ve formě modulů. Moduly Azure IoT Edge jsou nejmenší výpočetní jednotky spravuje IoT Edge a může obsahovat službám Microsoftu (jako je Azure Stream Analytics), 3. stran služby nebo kódu specifické pro řešení. Další informace o moduly IoT Edge najdete v tématu [moduly pochopit Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaký je rozdíl mezi typem kontejneru nabídky a nabídky typu modulu IoT Edge?**

Typ nabídky modul IoT Edge je konkrétní typ kontejneru spuštěného v zařízení IoT Edge. Součástí výchozí konfigurace nastavení na spouštění v kontextu IoT Edge a volitelně používá modul IoT Edge SDK integraci s modulem runtime IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikování modul IoT Edge

**Výběr správné výkladní skříň**

Moduly IoT Edge jsou pouze publikována na webu Azure Marketplace, AppSource se nedá použít.  Další informace o rozdílech a cílová skupina napříč prodejních míst najdete v tématu [určení možnost publikování pro vaše řešení](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Možnosti fakturace**

Na webu marketplace v současné době podporuje **Free** a **používání vlastní licence (BYOL)** možností pro moduly IoT Edge fakturace.
 
**Možnosti publikování**

Ve všech případech by měl vybrat moduly IoT Edge **Transact** publikování možnost.  Zobrazit [zvolte možnost publikování](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) podrobné informace o možnosti publikování.  

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Všechny podmínky smlouvy Microsoft Azure Marketplace a zásad se vztahují na nabídky modul IoT Edge.  Kromě toho existují požadavcích a technické požadavky pro moduly IoT Edge.  

**Předpoklady**

Modul IoT Edge publikovat na webu Azure Marketplace, musíte splňovat následující požadavky:

- Přístup na portál Cloud Partner (CPP). Další informace najdete v tématu [Průvodce publikováním webu Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostování modulu IoT Edge ve službě Azure Container Registry. 
- Máte připravený, jako jsou (mimo vyčerpávající seznam) metadata modulu IoT Edge: 
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevnou velikostí, včetně 40x40px 90x90px, 115x115px, 255x115px)
    - Období použití a ochrana osobních údajů zásad
    - Výchozí konfigurace modulu (trasy, požadované vlastnosti dvojčete, CreateOptions field, proměnné prostředí)
    - Dokumentace
    - Kontaktní údaje podpory

**Technické požadavky**

Primární technické požadavky pro modul IoT Edge v pořadí pro něj získat certifikaci a publikování na webu Azure Marketplace, jsou podrobně popsané na [modul IoT Edge včetně procesu certifikace](https://cloudpartner.azure.com/#documentation/iot-edge-module-certification-process) na [publikování cloudu Portál](https://cloudpartner.azure.com/).  

## <a name="documentation-and-resources"></a>Dokumentace a prostředky

Následující články jsou k dispozici, když jste přihlášeni [portálu pro publikování cloudu](https://cloudpartner.azure.com/):

- [Vytvoření nabídky modul IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – – s portálem publikování cloudu nabízejí kroky pro publikování nového modulu IoT Edge.
- [Proces certifikace modul IoT Edge](https://cloudpartner.azure.com/#documentation/iot-edge-module-certification-process) – přehled kroky a požadavky na certifikaci modul IoT Edge.
- [Modul IoT Edge – nejčastější dotazy](https://cloudpartner.azure.com/#documentation/iot-edge-module-faq) –-seznam nejčastějších dotazů týkající se moduly IoT Edge.

## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili,

- Registrace v [programu Microsoft Partner Network](https://partner.microsoft.com/membership).
- Vytvoření [Account Microsoft](https://account.microsoft.com/account/) (požadovaných pro Azure Marketplace transact nabídek; doporučuje pro ostatní uživatele).
- Odeslání [Marketplace registrační formulář](https://azuremarketplace.microsoft.com/sell/signup).

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/) k vytvoření nebo dokončení vaší nabídky.
