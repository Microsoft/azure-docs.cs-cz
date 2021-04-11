---
title: Certifikace svazků zařízení a nepřímých připojených zařízení
titleSuffix: Azure Certified
description: Podívejte se, jak odeslat nepřímo připojené zařízení k certifikaci.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: da3110b562bc5ddbd37657f31cbdd3790a13b897
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969214"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Sady prostředků zařízení a nepřímo připojená zařízení

Aby bylo možné podporovat zařízení, která komunikují s Azure, prostřednictvím nabídek zařízení, SaaS nebo PaaS, našeho portálu pro odesílání ( https://www.certify.azure.com) a katalogu zařízení ( https://devicecatalog.azure.com) povolením konceptů sdružování a závislostí a povolením těchto kombinací těchto zařízení získáte přístup k našemu programu zařízení s certifikací Azure.

V závislosti na vaší produktové lince a službách může vaše situace vyžadovat kombinaci těchto kroků:


![Vytvořit závislosti projektu](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Senzory a nepřímá zařízení
Řada senzorů vyžaduje, aby se zařízení připojilo k Azure. Kromě toho může být k dispozici více kompatibilních zařízení, která budou pracovat se zařízením snímače. **Aby se tyto scénáře vešly, musíte nejdřív zařízení certifikovat, než vydáte senzor, který jim přes ně předají informace.**

Příklad matice pro odeslání kombinací kombinací odeslání ![](./media/indirect-connected-device/picture-2.png )

K certifikaci vašeho snímače, který vyžaduje samostatné zařízení:
1.  Nejdřív [zařízení certifikovat](https://certify.azure.com) a publikujte do katalogu zařízení s certifikací Azure.
    - Pokud máte více kompatibilních předávacích zařízení (jako v příkladu výše), pošlete je samostatně pro certifikaci a publikovat do katalogu také.
2.  Pomocí snímače připojeného přes zařízení odešlete senzor k certifikaci.
    * Na kartě závislosti v části Podrobnosti o zařízení nastavte následující hodnoty.
        * Typ závislosti = "brána hardwaru"
        * Adresa URL závislosti = "odkaz na zařízení v katalogu zařízení"
        * Používá se během testování = "Ano"
        * Přidejte všechny komentáře směřující na zákazníka, které by měly být poskytnuty uživateli, který uvidí Popis produktu v katalogu zařízení. (příklad: zařízení řady 100 se vyžadují pro senzory připojení k Azure.)

3.  Pokud máte více zařízení, která chcete přidat jako volitelné pro toto zařízení, můžete vybrat "+ přidat další závislost". Pak postupujte podle stejných pokynů a Všimněte si, že se během testování nepoužil. V zákaznických komentářích zajistěte, aby si vaši zákazníci věděli, že jsou k tomuto senzoru k dispozici další zařízení (jako alternativa k zařízení, které bylo použito při testování).

![Alternativní text](./media/indirect-connected-device/picture-3.png "Typ závislosti hardwaru")

## <a name="paas-and-saas-offerings"></a>Nabídky PaaS a SaaS
V rámci vašeho portfolia produktů můžete mít zařízení, která certifikovat, ale vaše zařízení vyžaduje i další služby od vaší společnosti nebo jiných společností třetích stran. K přidání této závislosti použijte následující postup:
1. Spuštění procesu odeslání pro vaše zařízení
2. Na kartě závislosti nastavte následující hodnoty.
    - Dependency Type = "software Service"
    - Název služby = "[název vašeho produktu]"
    - Dependency URL = "odkaz URL na stránku produktu, která popisuje službu"
    - Přidejte libovolné zákaznické komentáře, které by se měly poskytnout uživateli, který uvidí Popis produktu v katalogu zařízení s certifikací Azure.
3. Pokud máte další software, služby nebo závislosti hardwaru, které byste chtěli přidat jako volitelné pro toto zařízení, můžete vybrat + přidat další závislost a postupovat podle stejných pokynů.

![Typ závislosti softwaru](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Rozbalené produkty
Seskupené výpisy produktů jsou jednoduše úspěšné certifikace zařízení s jinými součástmi, které budou prodávány jako součást sady v jednom seznamu produktů. Máte možnost Odeslat zařízení, které zahrnuje dodatečné komponenty, jako je například senzor teploty a senzor kamery (#1), nebo můžete odeslat dotykový senzor, který obsahuje průchozí zařízení (#2). Prostřednictvím funkce Component máte možnost přidat do svého seznamu více součástí.

Pokud máte v úmyslu to udělat, naformátujte image výpisu produktu tak, aby označovala, že tento produkt je součástí dalších komponent.  Pokud navíc vaše sada vyžaduje další služby k certifikaci, budete je muset identifikovat prostřednictvím závislosti služeb.
Příklad matice sad produktů

![Příklad odeslání sady prostředků](./media/indirect-connected-device/picture-5.png )

Podrobnější popis způsobu použití funkce komponenty na portálu zařízení s certifikací Azure najdete v naší [dokumentaci k nápovědě](./how-to-using-the-components-feature.md). 

Pokud je zařízení průchozí zařízení se samostatným senzorem ve stejném produktu, vytvořte jednu komponentu, aby odrážela průchozí zařízení, a další komponentu, která odráží senzor. Komponenty lze do projektu přidat na kartě Podrobnosti o produktu v části Podrobnosti o zařízení:

![Přidávání součástí](./media/indirect-connected-device/picture-6.png )

Pro průchozí zařízení nastavte typ součásti jako produkt připravený pro zákazníka a vyplňte ostatní pole, která jsou pro váš produkt relevantní. Příklad:

![Podrobnosti součásti](./media/indirect-connected-device/picture-7.png )

Pro senzor přidejte druhou komponentu, jako typ komponenty nastavte jako metodu periferní a Attachment jako diskrétní. Příklad:

![Podrobnosti o druhé součásti](./media/indirect-connected-device/picture-8.png )

Po vytvoření komponenty snímače upravte podrobnosti, přejděte na kartu senzory a přidejte podrobnosti snímače. Příklad:

![Podrobnosti snímače](./media/indirect-connected-device/picture-9.png )

Dokončete podrobnosti svých projektů a odešlete zařízení pro certifikaci jako normální.

