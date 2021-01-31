---
title: Nasazení Brána VP role virtuálního počítače s Windows rolí – Azure
description: Postup nasazení role Brána VP na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71bd7d38727d99c05a15c54e5141c613960d9050
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220815"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Nasazení role Brána VP na virtuálním počítači s Windows (Preview)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku se dozvíte, jak pomocí role Brána VP (Preview) nasadit Brána vzdálené plochy servery ve vašem prostředí. Role serveru můžete nainstalovat na fyzické počítače nebo virtuální počítače v závislosti na tom, jestli vytváříte místní, cloudové nebo hybridní prostředí.

## <a name="install-the-rd-gateway-role"></a>Instalace role Brána VP

1. Přihlaste se k cílovému serveru s přihlašovacími údaji správce.

2. V **Správce serveru** vyberte **Spravovat** a pak vyberte **Přidat role a funkce**. Otevře se instalační program **role a funkce pro přidání** .

3. **Než začnete**, vyberte **Další**.

4. V **Vyberte typ instalace** vyberte **instalace na základě rolí nebo na základě funkcí** a pak vyberte **Další**.

5. V části **Vybrat cílový server** vyberte **Vybrat server z fondu serverů**. V poli **Fond serverů** vyberte název místního počítače. Až budete hotovi, vyberte **Další**.

6. Na portálu **Vybrat role serveru**  >  vyberte **Služba Vzdálená plocha**. Až budete hotovi, vyberte **Další**.

7. Ve **službě Vzdálená plocha** vyberte **Další.**

8. U **možnosti vybrat služby rolí** vyberte pouze **Brána vzdálené plochy** po zobrazení výzvy k přidání požadovaných funkcí vyberte **Přidat funkce**. Až budete hotovi, vyberte **Další**.

9. U **služby síťové zásady a přístup** vyberte **Další**.

10. V případě **role webového serveru (IIS)** vyberte **Další**.

11. V poli **Vybrat služby rolí** vyberte **Další**.

12. Pro možnost **Potvrdit vybrané možnosti instalace** vyberte **nainstalovat**. Během procesu instalace nezavírejte instalační program.

## <a name="configure-rd-gateway-role"></a>Konfigurace role Brána VP

Jakmile je role Brána VP nainstalovaná, budete ji muset nakonfigurovat.

Postup konfigurace Brána VP role:

1. Otevřete **Správce serveru** a pak vyberte **Služba Vzdálená plocha**.

2. Přejděte na **servery**, klikněte pravým tlačítkem na název serveru a vyberte **Správce brány VP**.

3. V Správce brány VP klikněte pravým tlačítkem na název brány a pak vyberte **vlastnosti**.

4. Otevřete kartu **certifikát SSL** , zaškrtněte políčko **importovat certifikát do bubliny Brána VP** a pak vyberte **Procházet a importovat certifikát...**.

5. Vyberte název souboru PFX a pak vyberte **otevřít**.

6. Po zobrazení výzvy zadejte heslo k souboru PFX.

7. Po importu certifikátu a jeho privátního klíče by se v zobrazení mělo zobrazit klíčové atributy certifikátu.

>[!NOTE]
>Vzhledem k tomu, že Brána VP role by měla být veřejná, doporučujeme použít veřejně vydaný certifikát. Pokud používáte soukromý vydaný certifikát, bude nutné, abyste měli jistotu, že jste předem nakonfigurovali všechny klienty s řetězcem důvěryhodnosti certifikátu.

## <a name="next-steps"></a>Další kroky

Pokud chcete přidat vysokou dostupnost do role Brána VP, přečtěte si téma [Přidání vysoké dostupnosti do webu a webové služby vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).