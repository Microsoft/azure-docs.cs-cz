---
title: Exportovat nákladová data pomocí Azure Storage klíče SAS účtu
description: Tento článek pomáhá partnerům vytvořit klíč SAS a nakonfigurovat Cost Management exporty.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509660"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Exportovat nákladová data pomocí Azure Storage klíče SAS účtu

Následující informace platí jenom pro partnery Microsoftu.

Partneři mají často vlastní předplatné Azure v tenantovi, které je přidružené ke své vlastní smlouvě s partnerem Microsoftu. Partneři s plánem smlouvy o partnerovi Microsoftu, kteří jsou globálními správci svého fakturačního účtu, mohou exportovat a kopírovat nákladová data do účtu úložiště v jiném tenantovi pomocí klíče služby Shared Access Service (SAS). Jinými slovy účet úložiště s klíčem SAS umožňuje partnerovi používat pro příjem exportovaných informací účet úložiště, který je mimo svou partnerskou smlouvu. Tento článek pomáhá partnerům vytvořit klíč SAS a nakonfigurovat Cost Management exporty.

## <a name="requirements"></a>Požadavky

- Musíte být partnerem se smlouvou Microsoft Partner Agreement a mít zákazníky s plánem Azure.
- Musíte být globálním správcem fakturačního účtu vaší partnerské organizace.
- Musíte mít přístup ke konfiguraci účtu úložiště, který je v jiném tenantovi vaší partnerské organizace. Zodpovídáte za správu oprávnění a přístupu k datům, když exportujete data do svého účtu úložiště.

## <a name="configure-azure-storage-with-a-sas-key"></a>Konfigurace Azure Storage s klíčem SAS

Získejte token SAS účtu úložiště nebo ho vytvořte pomocí Azure Portal. K vytvoření v Azure Portal použijte následující postup. Další informace o klíčích SAS najdete v tématu [udělení omezeného přístupu k datům pomocí sdílených přístupových podpisů (SAS).](../../storage/common/storage-sas-overview.md)

1. V Azure Portal přejděte na účet úložiště.
    - Pokud má váš účet přístup k více klientům, přepněte adresáře pro přístup k účtu úložiště. V pravém horním rohu Azure Portal vyberte svůj účet a pak vyberte **Přepnout adresáře**.
    - Pro přístup k účtu úložiště se možná budete muset přihlásit k Azure Portal pomocí odpovídajícího účtu tenanta.
1. V nabídce vlevo vyberte **sdílený přístupový podpis**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Snímek obrazovky s nakonfigurovaným sdíleným přístupovým podpisem úložiště Azure" lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Nakonfigurujte token se stejným nastavením, jak je uvedeno na předchozím obrázku.
    1. Pro _povolené služby_ vyberte **objekt BLOB** .
    1. Pro _Povolené typy prostředků_ vyberte **službu**, **kontejner** a **objekt** .
    1. Pro _povolená oprávnění_ vyberte **číst**, **zapsat**, **Odstranit**, **vypsat**, **Přidat** a **vytvořit** .
    1. Vyberte datum a čas. Před vypršením platnosti nezapomeňte aktualizovat svůj token SAS pro export. Čím delší je časové období, které nakonfigurujete před vypršením platnosti, tím déle bude export běžet před tím, než budete potřebovat nový token SAS.
1. Vyberte **https jenom** pro _Povolené protokoly_.
1. Vyberte **základní** pro _upřednostňovanou úroveň směrování_.
1. Pro _podpisový klíč_ vyberte **klíč1** . Pokud otočíte nebo aktualizujete klíč, který se používá k podepsání tokenu SAS, budete muset pro export znovu vygenerovat nový token SAS.
1. Vyberte **Vygenerovat SAS a připojovací řetězec**.
    Zobrazená hodnota **tokenu SAS** je token, který budete potřebovat při konfiguraci exportů.

## <a name="create-a-new-export-with-a-sas-token"></a>Vytvoření nového exportu pomocí tokenu SAS

Přejděte k **exportům** v oboru fakturačního účtu a vytvořte nový export pomocí následujících kroků.

1. Vyberte **Vytvořit**.
1. Nakonfigurujte Podrobnosti exportu jako při normálním exportu. Export můžete nakonfigurovat tak, aby používal existující adresář nebo kontejner, nebo můžete zadat nový adresář nebo kontejner a export je vytvoří za vás.
1. Při konfiguraci úložiště vyberte **použít token SAS**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Snímek obrazovky s novým exportem, ve kterém jste vybrali token SAS" lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Zadejte název účtu úložiště a vložte ho do svého tokenu SAS.
1. Zadejte existující kontejner nebo adresář nebo Identifikujte nové, které se mají vytvořit.
1. Vyberte **Vytvořit**.

Export založený na tokenech SAS funguje pouze v případě, že token zůstává platný. Resetování tokenu před vypršením platnosti aktuálního intervalu nebo váš export přestane fungovat. Vzhledem k tomu, že token poskytuje přístup k vašemu účtu úložiště, Chraňte token hned stejně jako jakékoli jiné citlivé informace. Zodpovídáte za správu oprávnění a přístupu k datům, pokud exportujete data do svého účtu úložiště.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Řešení potíží s exporty pomocí tokenů SAS

Níže jsou uvedené běžné problémy, ke kterým může dojít při konfiguraci nebo použití exportů na základě tokenů SAS.

- V Azure Portal se nezobrazuje možnost klíč SAS.
  - Ověřte, že jste partnerem se smlouvou Microsoft a že máte k fakturačnímu účtu oprávnění globálního správce. Jsou to jenom lidé, kteří můžou exportovat pomocí klíče SAS.

- Při pokusu o konfiguraci exportu se zobrazí následující chybová zpráva:

    **Ujistěte se prosím, že token SAS je platný pro službu BLOB Service, je platný pro typy prostředků kontejneru a objektu a má oprávnění: Přidat a číst zápis pro čtení. (Kód chyby služby Storage: AuthorizationResourceTypeMismatch)**

    - Ujistěte se, že jste správně nakonfigurovali a vygenerovali klíč SAS v Azure Storage.

- Po vytvoření exportu nemůžete zobrazit celý klíč SAS.
  - Nezobrazuje se očekávané chování klíče. Po nakonfigurování exportu SAS je klíč z bezpečnostních důvodů skrytý.

- Nemůžete získat přístup k účtu úložiště z tenanta, ve kterém je export nakonfigurovaný.
  - Očekává se chování. Pokud je účet úložiště v jiném tenantovi, musíte přejít na tohoto tenanta jako první v Azure Portal a najít účet úložiště.

- Export se nezdařil z důvodu chyby související s tokenem SAS.
  - Váš Export funguje jenom v případě, že token SAS zůstává platný. Vytvořte nový klíč a spusťte export.

## <a name="next-steps"></a>Další kroky

- Další informace o exportu Cost Management dat najdete v tématu [Vytvoření a export dat](tutorial-export-acm-data.md).
- Informace o exportu velkých objemů dat o využití najdete v tématu [načtení velkých datových sad s exporty](ingest-azure-usage-at-scale.md).
