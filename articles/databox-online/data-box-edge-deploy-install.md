---
title: Kurz o fyzické zařízení Azure Data Box Edge instalaci | Dokumentace Microsoftu
description: Druhé části kurzu o instalaci Azure Data Box Edge zahrnuje jak Vybalte, namontujte do racku a fyzickým zařízením.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 243697c20f10a019a73326c3bd79e23fc3342640
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113596"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Kurz: Nainstalovat Azure Data Box Edge (preview)

Tento kurz popisuje, jak nainstalovat fyzické zařízení Data Box Edge. Postup instalace zahrnuje při rozbalování, montáže do racku a kabeláž zařízení. 

Instalace může trvat přibližně dvě hodiny pro dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Namontovat zařízení
> * Zapojení kabeláže zařízení

> [!IMPORTANT]
> Data Box Edge řešení je ve verzi preview. Než pořadí a nasazení tohohle řešení, projděte si [podmínkami pro verzi preview služby Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="prerequisites"></a>Požadavky

Předpoklady pro instalaci fyzické zařízení následujícím způsobem:

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [Příprava na nasazení Azure Data Box Edge (preview)](data-box-edge-deploy-prep.md).
    * Vytvořili jste prostředek Data Box Edge do zařízení nasadit.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení s použitím prostředku Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Fyzické zařízení Data Box Edge

Před nasazením zařízení:

- Ujistěte se, že zařízení bezpečně postavená na bez stromové struktury, stabilní a úrovni pracovní plocha.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní napájení z nezávislých zdroje

        - nebo -
    - Jednotka distribuci napájení (PDU) rack s nepřerušitelný zdroj napájení (UPS)
    - K dispozici 1U slot na stojan, na který chcete připojit zařízení

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Zkontrolujte požadavky na síť pro nasazení hrany pole Data a nakonfigurujte síť datacenter konkrétní požadavky. Další informace najdete v tématu popisujícím [požadavky na síť pro Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířky pásma Internetu je 20 MB/s pro optimální fungování zařízení.


## <a name="unpack-the-device"></a>Rozbalení zařízení

Toto zařízení se dodává v jedné krabici. Zařízení rozbalíte pomocí následujících kroků. 

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud pole nebo balení je vážně poškozen, neotevírejte ho. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.
3. Rozbalte krabici. Po rozbalení krabice se ujistěte, že obsahuje následující:
    - Jedno zařízení Edge v jedné skříni
    - Dva napájecí kabely
    - Jeden snímek bez nástroje do racku kit (dvě na straně rails a hardware připojení jsou zahrnuty)

Pokud jste nedostali všechny položky, které jsou tady uvedené, obraťte se na podporu Data Box Edge. Dalším krokem je stojan připojení vašich zařízení.


## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení musí být nainstalován na standardní 19 palec stojanu. Pomocí následujícího postupu do racku připojení vašeho zařízení na standardní stojanu 19 palce na přední a zadní příspěvky.

> [!IMPORTANT]
> Aby zařízení Data Box Edge správně fungovala, musí být usazená do racku.


1. Zatáhněte za přední uvolňovací mechanismus a uvolněte vnitřní kolejnici od výsuvné lišty. Uvolněte zarážku a zatlačte prostřední kolejnici dovnitř, abyste kolejnici mohli vytáhnout.  
    Vnitřní a vnější kolejnice by teď měly být oddělené.

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Nainstalujte vnější rails CAB svislé členy stojanu. Abyste při orientaci, jsou označeny snímky lišty **Front**, a proto je směrem dopředu skříň.    
    1. Vyhledejte čepy v přední a zadní části montážní lišty. Roztáhněte kolejnici tak, aby se vešla mezi sloupky racku. Nejprve připevněte vnější kolejnici do zadní části racku. Upravte zadní připojení závorky umístěte do zadní děr montáže do racku.   

    2. Stlačte a přidržte západku na zadním držáku, aby se odhalily kovové háčky. Zarovnat a vložit zpět závorky do otvorů připojení a uvolněte aktivační událost.

    3. Zarovnejte přední držák s montážním otvorem.

    4. Přední závorky by měl nyní pevného v racku. Volitelně můžete M5 X 10L šrouby slouží k zabezpečení rails s příspěvky v případě potřeby. 

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Připojit vnitřní lišty ve skříni, ujistěte se, že klíčové dírky otvory na vnitřní lišty jsou v souladu s vyhledáním PIN kódy Toolbar skříni. Zkontrolujte, že hlavičky pomocných čepů na skříni procházejí otvory ve vnitřní kolejnici. Vytáhněte kolejnici směrem dopředu skříně, dokud kolejnice se slyšitelným cvaknutím nezapadne na místo. To samé zopakujte i s druhou vnitřní kolejnicí. Dokončete instalaci do racku tím, že zasunete skříň s vnitřní kolejnicí do vodicí lišty.

    ![Montáž kolejnic pro usazení do racku](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Následující postupy vysvětlují, jak zapojit napájecí a síťový kabel zařízení Edge.

Než začnete kabeláže zařízení, budete potřebovat následující:

- Rozbalené fyzické zařízení Edge usazené do racku
- Dva napájecí kabely 
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- Jeden měděný kabel 25 GbE SFP+ pro každé datové síťové rozhraní, které chcete konfigurovat. Nejméně jeden datový síťové rozhraní z PORT 2, PORT 3, PORT 4, PORT 5 nebo 6 portu musí být připojený k Internetu (s připojením k Azure).  
- Přístup ke dvěma jednotek pro distribuci napájení (doporučeno).

> [!NOTE]
> - Pokud připojujete pouze jedno datové síťové rozhraní, doporučujeme k odesílání dat do Azure použít síťové rozhraní 25 GbE, jako je PORT 3, PORT 4, PORT 5 nebo PORT 6. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Zařízení Edge musí být připojené k síti datacentra, aby mohlo ingestovat data ze serverů zdroje dat. 

Vaše zařízení Edge má 8 disků SSD NVMe. Přední panel obsahuje také indikátory LED stavu a tlačítka napájení. Zařízení obsahuje redundantní napájení dodavatelského jednotek (PSUs) na pozadí. Vaše zařízení má 6 síťových rozhraní: dvě rozhraní 1 Gb/s a čtyři rozhraní 25 Gb/s. Vaše zařízení obsahuje kontroler BMC (Baseboard Management Controller). Identifikujte různé porty na propojovacím rozhraní vašeho zařízení.
 
  ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Proveďte následující kroky a zapojení kabeláže zařízení pro napájení a sítě.

1. Připojte napájecí kabely k oběma jednotkám PSU ve skříni. Pokud chcete zajistit vysokou dostupnost, nainstalujte a připojte obě jednotky k různým zdrojům napájení.

2. Připojte napájecí kabely k rozvodným energetickým jednotkám racku. Ujistěte se, že obě jednotky PSU používají samostatné zdroje napájení.

3. Rozhraní sítě 1 GbE PORT 1 připojení k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.

4. Připojte jeden nebo několik portů PORT 2, PORT 3, PORT 4, PORT 5 nebo PORT 6 k internetu nebo síti datacentra. Pokud připojujete PORT 2, použijte síťový kabel RJ-45. V případě síťových rozhraní 25 GbE použijte měděné kabely SFP+.  


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Data Box Edge témata naučili jste se například:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení dat pole Edge](./data-box-edge-deploy-connect-setup-activate.md)


