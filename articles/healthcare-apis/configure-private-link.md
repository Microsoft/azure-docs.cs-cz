---
title: Privátní odkaz pro Azure API pro FHIR
description: Tento článek popisuje, jak nastavit privátní koncový bod pro Azure API pro služby FHIR Services.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: 121ddcc013fbeef29c6d22cfbd273cfd3b8d3d98
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558891"
---
# <a name="configure-private-link"></a>Konfigurace privátního odkazu

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Privátní odkaz vám umožní přístup k Azure API pro FHIR přes soukromý koncový bod, síťové rozhraní, které vás připojuje soukromě a bezpečně pomocí privátní IP adresy z vaší virtuální sítě. Pomocí privátního odkazu můžete bezpečně přistupovat k našim službám z vaší virtuální sítě jako služby First Service, aniž byste museli projít veřejnou službou DNS. Tento článek vás provede vytvořením, otestováním a správou privátního koncového bodu pro Azure API pro FHIR.

## <a name="prerequisites"></a>Požadavky

Před vytvořením privátního koncového bodu jsou k dispozici některé prostředky Azure, které budete muset vytvořit jako první:

- Skupina prostředků – skupina prostředků Azure, která bude obsahovat virtuální síť a soukromý koncový bod.
- Azure API pro FHIR – prostředek FHIR, který byste chtěli umístit za soukromý koncový bod.
- Virtual Network – virtuální síť, ke které budou připojené vaše klientské služby a privátní koncový bod.

Další informace najdete v [dokumentaci k privátnímu odkazu](https://docs.microsoft.com/azure/private-link/).

## <a name="disable-public-network-access"></a>Zakázat přístup k veřejné síti

Vytvořením privátního koncového bodu pro prostředek FHIR se do něj automaticky nezakáže veřejný provoz. K tomu je potřeba aktualizovat prostředek FHIR, aby se nastavila nová vlastnost Public Access z Enabled na Disabled (zakázáno). Buďte opatrní, pokud zakážete přístup k veřejné síti, protože všechny požadavky na službu FHIR, které nepřicházejí z správně nakonfigurovaného privátního koncového bodu, budou odepřeny. Povolí se jenom provoz z privátních koncových bodů.

![Zakázat přístup k veřejné síti](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

Pro vytvoření privátního koncového bodu může vývojář s oprávněními RBAC na prostředku FHIR používat Azure Portal, [Azure PowerShell](https://docs.microsoft.com/azure/private-link/create-private-endpoint-powershell)nebo [Azure CLI](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli). Tento článek vás provede kroky k použití Azure Portal. Použití Azure Portal se doporučuje, protože automatizuje vytváření a konfiguraci Privátní DNS zóny. Další podrobnosti najdete na stránce s přehledem [rychlé zprovoznění privátních odkazů](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal) .

Existují dva způsoby, jak vytvořit soukromý koncový bod. Tok automatického schvalování umožňuje uživateli, který má oprávnění RBAC pro prostředek FHIR, vytvořit soukromý koncový bod bez nutnosti schválení. Tok ručního schválení umožňuje uživateli bez oprávnění k FHIR prostředku požádat o schválení privátního koncového bodu vlastníky prostředku FHIR.

### <a name="auto-approval"></a>Automatické schválení

Ujistěte se, že je oblast nového privátního koncového bodu shodná s oblastí pro váš Virtual Network. Oblast pro prostředek FHIR se může lišit.

![Karta základy Azure Portal](media/private-link/private-link-portal2.png)

Jako typ prostředku vyhledejte a vyberte Microsoft. HealthcareApis/Services. Jako prostředek vyberte prostředek FHIR. V části cílový dílčí prostředek vyberte "fhir".

![Karta prostředku Azure Portal](media/private-link/private-link-portal1.png)

Pokud nemáte nastavenou stávající zónu Privátní DNS, vyberte "(nové) privatelink. azurehealthcareapis. com". Pokud již máte nakonfigurovanou zónu Privátní DNS, můžete ji vybrat ze seznamu. Musí být ve formátu "privatelink.azurehealthcareapis.com".

![Karta konfigurace Azure Portal](media/private-link/private-link-portal3.png)

Po dokončení nasazení se můžete vrátit na kartu "připojení privátních koncových bodů", na které se zobrazí zpráva "Schváleno" jako stav připojení.

### <a name="manual-approval"></a>Ruční schválení

V případě ručního schválení vyberte druhou možnost v části prostředek, "připojit k prostředku Azure podle ID prostředku nebo aliasu". Jako cílový dílčí prostředek zadejte "fhir" jako v rámci automatického schvalování.

![Ruční schválení](media/private-link/private-link-manual.png)

Až se nasazení dokončí, můžete se vrátit na kartu "připojení privátních koncových bodů", na kterých můžete připojení schválit, odmítnout nebo odebrat.

![Možnosti](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Test privátního koncového bodu

Abyste se ujistili, že server FHIR nepřijímá veřejný provoz po zakázání přístupu k veřejné síti, zkuste použít koncový bod/metadata pro váš server z počítače. Měli byste obdržet 403 zakázaný. Všimněte si, že může trvat až 5 minut od aktualizace příznaku přístupu k veřejné síti před zablokováním veřejného provozu.

Abyste se ujistili, že váš privátní koncový bod může odesílat přenosy na váš server:

1. Vytvořte virtuální počítač, který je připojený k virtuální síti, a podsíť, na které je váš privátní koncový bod nakonfigurovaný. Pokud chcete zajistit, aby provoz z virtuálního počítače používal jenom privátní síť, můžete odchozí internetovou komunikaci zakázat prostřednictvím pravidla NSG.
2. Protokol RDP do virtuálního počítače.
3. Zkuste použít koncový bod/metadata serveru FHIR z virtuálního počítače, měli byste obdržet příkaz schopnosti jako odpověď.

## <a name="manage-private-endpoint"></a>Správa privátního koncového bodu

### <a name="view"></a>Zobrazit

Privátní koncové body a přidružená síťová karta jsou v Azure Portal viditelné ze skupiny prostředků, ve které byly vytvořeny.

![Zobrazit v prostředcích](media/private-link/private-link-view.png)

### <a name="delete"></a>Odstranit

Soukromé koncové body lze odstranit pouze z Azure Portal prostřednictvím okna Přehled (jak je uvedeno níže) nebo pomocí možnosti Odstranit v části sítě (Preview) "připojení privátních koncových bodů". Kliknutím na tlačítko Odstranit odstraníte soukromý koncový bod a příslušný síťový adaptér. Pokud odstraníte všechny privátní koncové body pro prostředek FHIR a přístup k veřejné síti je zakázaný, nebude žádný požadavek na váš server FHIR provádět. Aby bylo možné odstranit nebo přesunout prostředek FHIR, musí být z prostředku FHIR odstraněny všechny privátní koncové body.

![Odstranit privátní koncový bod](media/private-link/private-link-delete.png)
