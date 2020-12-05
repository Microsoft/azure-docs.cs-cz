---
title: Koncepty sad replik pro Azure AD Domain Services | Microsoft Docs
description: Zjistěte, jaké sady replik jsou v Azure Active Directory Domain Services a jak poskytují redundanci aplikacím, které vyžadují služby identity.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
ms.openlocfilehash: 5359a955ea97b559b7e3d244bfb6c4fb09e8681b
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620031"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>Klíčové pojmy a funkce pro Azure Active Directory Domain Services sady repliky (Preview)

Při vytváření spravované domény Azure Active Directory Domain Services (Azure služba AD DS) můžete definovat jedinečný obor názvů. Tento obor názvů je název domény, třeba *aaddscontoso.com*, a dva řadiče domény (DCS) se pak nasadí do vybrané oblasti Azure. Toto nasazení řadičů domény se označuje jako sada replik.

Spravovanou doménu můžete rozšířit tak, aby měla více než jednu sadu replik na jeden tenant služby Azure AD. Sady replik se dají přidat do všech partnerských virtuálních sítí v libovolné oblasti Azure, která podporuje Azure služba AD DS. Další sady replik v různých oblastech Azure poskytují geografickou obnovu po havárii pro starší aplikace, pokud oblast Azure přejde do režimu offline.

Sady replik jsou momentálně ve verzi Preview.

> [!NOTE]
> Sady replik neumožňují nasadit více jedinečných spravovaných domén v jednom tenantovi Azure. Každá sada replik obsahuje stejná data.

## <a name="how-replica-sets-work"></a>Jak fungují sady replik

Při vytváření spravované domény, jako je třeba *aaddscontoso.com*, se vytvoří počáteční sada replik. Další sady replik sdílejí stejný obor názvů a konfiguraci. Změny v Azure služba AD DS, včetně konfigurace, identity uživatele a přihlašovacích údajů, skupin, objektů zásad skupiny, počítačových objektů a dalších změn, se aplikují na všechny sady replik ve spravované doméně pomocí replikace služba AD DS.

Každou sadu replik vytvoříte ve virtuální síti. Každá virtuální síť musí být partnerského vztahu k každé jiné virtuální síti, která je hostitelem sady replik spravované domény. Tato konfigurace vytvoří síťovou topologii sítě, která podporuje replikaci adresářů. Virtuální síť může podporovat více sad replik za předpokladu, že každá sada replik je v jiné virtuální podsíti.

Všechny sady replik jsou umístěny ve stejné lokalitě služby Active Directory. V důsledku toho se všechny změny šíří pomocí replikace v rámci služby v rámci rychlé konvergence.

> [!NOTE]
> Není možné definovat samostatné lokality a definovat nastavení replikace mezi sadami replik.

Následující diagram znázorňuje spravovanou doménu se dvěma sadami replik. První sada replik je vytvořena s oborem názvů domény. Druhá sada replik se vytvoří po této:

![Diagram ukázkové spravované domény se dvěma sadami replik](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> Sady replik zajišťuje dostupnost služeb ověřování v oblastech, kde je nakonfigurovaná sada replik. Aby mohla aplikace mít geografickou redundanci v případě, že dojde k oblastnímu výpadku, musí být aplikační platforma, která závisí na spravované doméně, také umístěna v jiné oblasti.
>
> Sady replik neposkytují odolnost dalších služeb, které jsou nutné pro fungování aplikace, jako jsou například virtuální počítače Azure nebo Azure App Services. Návrh dostupnosti dalších komponent aplikace musí zvážit funkce odolnosti pro služby, které tvoří aplikaci.

Následující příklad ukazuje spravovanou doménu se třemi sadami replik pro další zajištění odolnosti a zajištění dostupnosti služeb ověřování. V obou příkladech se úlohy aplikací nacházejí ve stejné oblasti jako sada replik spravované domény:

![Diagram ukázkové spravované domény se třemi sadami replik](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Aspekty nasazování

Výchozí SKU pro spravovanou doménu je SKU *podniku* , které podporuje několik sad replik. Pokud chcete vytvořit další sady replik, pokud jste změnili na *standardní* SKU, [upgradujte spravovanou doménu](change-sku.md) na *Enterprise* nebo *Premium*.

Maximální počet sad replik podporovaných během verze Preview je čtyři, včetně první repliky vytvořené při vytváření spravované domény.

Faktura za každou sadu replik je založena na skladové jednotce konfigurace domény. Pokud máte například spravovanou doménu, která používá skladovou položku *Enterprise* a máte tři sady replik, vaše předplatné se účtuje za hodinu pro každou ze tří sad replik.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>Můžu v této verzi Preview použít spravovanou doménu v produkčním prostředí?

Sady replik jsou funkcí veřejné verze Preview v Azure AD Domain Services. Můžete použít produkční spravovanou doménu, ale uvědomte si prosím o rozdílech podpory, které existují pro funkce stále ve verzi Preview. Další informace o [verzi Preview najdete v Azure Active Directory verze Preview smlouvy SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Můžu v předplatném vytvořit sadu replik odlišnou od mé spravované domény?

No. Sady replik musí být ve stejném předplatném jako spravovaná doména.

### <a name="how-many-replica-sets-can-i-create"></a>Kolik sad replik můžu vytvořit?

Verze Preview je omezená na maximálně čtyři sady replik – počáteční sada replik pro spravovanou doménu a tři další sady replik.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Jak se budou informace o uživatelích a skupinách synchronizovat se sadami replik?

Všechny sady replik jsou vzájemně propojené pomocí partnerského vztahu virtuálních sítí sítě. Jedna sada replik přijímá aktualizace uživatelů a skupin ze služby Azure AD. Tyto změny se pak replikují do ostatních sad replik pomocí replikace z služba AD DS v rámci sítě s partnerským vztahem.

Stejně jako u místních služba AD DS může prodloužený odpojený stav způsobit přerušení replikace. V případě nepřenosných partnerských virtuálních sítí se požadavky na návrh pro sady replik vyžadují s úplnou síťovou topologií.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Návody udělat změny ve spravované doméně, I když mám sady replik?

Změny ve spravované doméně fungují stejně jako dříve. [Pomocí nástrojů pro vzdálenou správu serveru, který je připojený ke spravované doméně, vytvoříte a použijete virtuální počítač pro správu](tutorial-create-management-vm.md). V případě potřeby můžete ke spravované doméně připojit tolik virtuálních počítačů pro správu.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít se sadami replik, [vytvořte a nakonfigurujte spravovanou doménu Azure služba AD DS][tutorial-create-advanced]. Při nasazení [vytvořte a použijte další sady replik][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
