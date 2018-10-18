---
title: Kurz instalace fyzického zařízení Azure Data Box Edge | Microsoft Docs
description: Druhý kurz k instalaci Azure Data Boxu Edge se zabývá rozbalením fyzického zařízení, jeho usazením do racku a zapojením kabeláže.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 21ac3de793f5ce559c3a03de2a09f11ccb86b12a
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167354"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Kurz: Instalace Azure Data Boxu Edge (Preview)

Tento kurz popisuje, jak nainstalovat fyzické zařízení Data Box Edge. Postup instalace zahrnuje rozbalení zařízení, jeho usazení do racku a zapojení kabeláže. 

Dokončení instalace může trvat přibližně 2 hodiny.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Požadavky na instalaci fyzického zařízení jsou následující.

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v tématu [Příprava portálu pro Data Box Edge](data-box-edge-deploy-prep.md).
    * Vytvořili jste prostředek Data Box Edge pro nasazení vašeho zařízení.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení s použitím prostředku Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Fyzické zařízení Data Box Edge

Před nasazením zařízení:

- Ujistěte se, že je zařízení bezpečně umístěné na rovném a stabilním pracovním povrchu (nebo něčem podobném).
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Nezávislý zdroj standardního střídavého proudu
    - Rozvodná energetická jednotka racku s nepřerušitelným zdrojem napájení (UPS)
- Ujistěte se, že v racku, do kterého chcete zařízení usadit, je k dispozici slot 1U.

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Projděte si požadavky na síť pro nasazení Data Boxu Edge a nakonfigurujte podle těchto požadavků síť datacentra. Další informace najdete v tématu popisujícím [požadavky na síť pro Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).
- Ujistěte se, že je šířka internetového pásma minimálně 20 Mb/s, aby zařízení mohlo fungovat optimálně.


## <a name="unpack-the-device"></a>Rozbalení zařízení

Toto zařízení se dodává v jedné krabici. Zařízení rozbalíte pomocí následujících kroků. 

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud jsou krabice nebo obal vážně poškozené, krabici neotevírejte. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.
3. Rozbalte krabici. Po rozbalení krabice se ujistěte, že obsahuje následující:
    - Jedno zařízení Edge v jedné skříni
    - Dva napájecí kabely
    - Jedna sada pro usazení do racku zasunutím bez nástrojů (součástí balení jsou dvě boční kolejnice a montážní prvky)
4. Pokud jste některou z výše uvedených položek neobdrželi, kontaktujte podporu pro Data Box Edge. Dalším krokem je usazení zařízení do racku.


## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení je potřeba nainstalovat do standardního 19palcového racku. Pomocí následujícího postupu usaďte zařízení do standardního 19palcového racku s předním a zadním sloupkem.

> [!IMPORTANT]
> Aby zařízení Data Box Edge správně fungovala, musí být usazená do racku.


1. Zatáhněte za přední uvolňovací mechanismus a uvolněte vnitřní kolejnici od výsuvné lišty. Uvolněte zarážku a zatlačte prostřední kolejnici dovnitř, abyste kolejnici mohli vytáhnout. Vnitřní a vnější kolejnice by teď měly být oddělené.

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Teď namontujte vnější kolejnice ke svislým lištám skříně racku. Pro usnadnění orientace jsou vodicí lišty označené slovem Front (Přední) a takto označený konec se má připevnit do přední části skříně. 
    
    1. Vyhledejte čepy v přední a zadní části montážní lišty. Roztáhněte kolejnici tak, aby se vešla mezi sloupky racku. Nejprve připevněte vnější kolejnici do zadní části racku. Nastavte zadní nosné držáky a umístěte je do montážních otvorů v zadní části racku.   
    2. Stlačte a přidržte západku na zadním držáku, aby se odhalily kovové háčky. Kovové háčky srovnejte, vložte je do montážních otvorů a pak uvolněte západku.
    3. Zarovnejte přední držák s montážním otvorem.
    4. Přední držák by teď měl být upevněný v racku. Volitelně můžete k upevnění kolejnic ke sloupkům použít šroubky M5 X 10L. 

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Abyste mohli do skříně připevnit vnitřní kolejnici, ujistěte se, že jsou otvory na vnitřní kolejnici zarovnané s pomocnými čepy na straně skříně. Zkontrolujte, že hlavičky pomocných čepů na skříni procházejí otvory ve vnitřní kolejnici. Vytáhněte kolejnici směrem dopředu skříně, dokud kolejnice se slyšitelným cvaknutím nezapadne na místo. To samé zopakujte i s druhou vnitřní kolejnicí. Dokončete instalaci do racku tím, že zasunete skříň s vnitřní kolejnicí do vodicí lišty.

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Následující postupy vysvětlují, jak zapojit napájecí a síťový kabel zařízení Edge.

## <a name="prerequisites"></a>Požadavky

Než začnete zapojovat kabeláž zařízení, budete potřebovat následující:

- Rozbalené fyzické zařízení Edge usazené do racku
- Dva napájecí kabely 
- Dva síťové kabely 1 GbE RJ-45 a čtyři měděné kabely 25 GbE SFP+
- Přístup ke dvěma rozvodným energetickým jednotkám (doporučeno)

Vaše zařízení Edge má 8 disků SSD NVMe. Přední panel obsahuje také indikátory LED stavu a tlačítka napájení. Na zadní straně zařízení se nacházejí také redundantní jednotky PSU (Power Supply Unit). Vaše zařízení má 6 síťových rozhraní: dvě rozhraní 1 Gb/s a čtyři rozhraní 25 Gb/s. Vaše zařízení obsahuje kontroler BMC (Baseboard Management Controller). Identifikujte různé porty na propojovacím rozhraní vašeho zařízení.
 
  ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Při zapojování napájecího a síťového kabelu zařízení postupujte následovně.

1. Připojte napájecí kabely k oběma jednotkám PSU ve skříni. Pokud chcete zajistit vysokou dostupnost, nainstalujte a připojte obě jednotky k různým zdrojům napájení.
2. Připojte napájecí kabely k rozvodným energetickým jednotkám racku. Ujistěte se, že obě jednotky PSU používají samostatné zdroje napájení.
3. Připojte PORT 1 síťového rozhraní 1 GbE k počítači, který slouží ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
4. Pomocí síťových kabelů RJ-45 připojte PORT 2 síťového rozhraní 1 GbE k internetu nebo síti datacentra. 
5. Pomocí měděných kabelů SFP+ připojte PORT 3, PORT 4, PORT 5 a PORT 6 čtyř síťových rozhraní 25 GbE k internetu nebo síti datacentra. 

> [!NOTE]
> - Alespoň jedno datové síťové rozhraní (PORT 2, PORT 3, PORT 4, PORT 5 nebo PORT 6) musí být připojené k internetu (pro zajištění možnosti připojení k Azure). 
> - K odesílání dat do Azure doporučujeme použít síťová rozhraní 25 GbE, jako je PORT 3, PORT 4, PORT 5 nebo PORT 6. 
> - Zařízení Edge musí být připojené k síti datacentra, aby mohlo ingestovat data ze serverů zdroje dat.  


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení Data Boxu Edge](./data-box-edge-deploy-connect-setup-activate.md)


