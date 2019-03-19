---
title: Zabezpečení dat ve službě Box Edge | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání zařízení Data Box Edge, služby a data v místním prostředí i v cloudu.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e3a24117cfd01c1c0bd0f08e8eca5adddf5ee7b6
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2019
ms.locfileid: "58119781"
---
# <a name="data-box-edge-security-and-data-protection-preview"></a>Data Box Edge zabezpečení a ochranu dat (preview)

Zabezpečení představuje závažný problém při přijetí nové technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Řešení Microsoft Azure Data Box Edge pomáhá zajistit, že jen autorizované entity můžete zobrazit, upravit nebo odstranit data.

Tento článek popisuje funkce zabezpečení okraj pole dat, které pomáhají chránit všechny komponenty řešení a data uložená na ně.

Řešení Azure Data Box Edge se skládá z čtyři hlavní součásti, které spolu interagují:

- **Data Box Edge / brána pole dat služby hostované v Azure** – prostředku správy, který použijete k vytvoření objednávky zařízení, nakonfigurovat zařízení a pak sledovat pořadí do konce.
- **Zařízení data Box Edge** – přenos zařízení dodávané do import místních dat do Azure.
- **Klienty a hostitele připojené k zařízení** – klienti ve vaší infrastruktuře, připojte se k zařízení Data Box Edge a obsahující data, která se dají chránit.
- **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží. Toto umístění je obvykle účtu úložiště propojeném prostředku okraj pole dat, který jste vytvořili.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Než pořadí a nasazení tohohle řešení, projděte si [podmínkami pro verzi preview služby Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Ochrana služby pole Edge/Data Box brány dat

Služba Data Box Edge/Data pole brány je služba pro správu hostované v Microsoft Azure. Služba se používá ke konfiguraci a správě zařízení.

- Přístup ke službě Data Box Edge/Data pole brány vyžaduje vaše organizace má smlouvu Enterprise (EA) nebo v rámci předplatného Cloud Solution Provider (CSP). Další informace najdete v části [zaregistrovat předplatné Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Vzhledem k tomu, že vaše služba management je hostovaná v Azure, je chráněn funkce zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="data-box-edge-device-protection"></a>Ochrana dat pole hraniční zařízení

Data Box hraniční zařízení je místní zařízení, která pomáhá transformovat data pomocí zpracování místně a odesílá je do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge/Data pole brány.
- Je chráněn za všech okolností hesla správce zařízení.
- Je zařízení uzamknuté. Zařízení chráněném heslem s omezeným přístupem uživatele pro systém BIOS jsou BMC a systému BIOS.
- Má povolené zabezpečené spouštění.
- Spuštění Windows Defenderu Device Guard. Device Guard umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásadách integrity kódu. 

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivační kód

Pouze autorizovaného zařízení Data Box Edge je povolené pro připojení služby Data Box Edge/Data pole brány, kterou jste vytvořili ve vašem předplatném Azure. K ověření zařízení, musíte použít aktivační kód k aktivaci zařízení pomocí služby Data Box Edge/Data pole brány. Další informace najdete v části [získat aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key).

Aktivační kód, který používáte:

- Představuje kód ověřování na základě Azure Active Directory (AAD).
- Platnost vyprší po třech dnech.
- Po aktivaci zařízení se nepoužívá.
 
Po aktivaci zařízení používá tokeny ke komunikaci s Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Ochrana zařízení pomocí hesla

Hesla Ujistěte se, že vaše data jsou přístupné pouze oprávněným uživatelům. Okraj pole data a Data Box brány zařízení spuštění v uzamčeném stavu.

Můžete:

- Připojení k místní webové uživatelské rozhraní zařízení přes prohlížeč a zadejte heslo pro přihlášení na zařízení.
- Vzdálené připojení k rozhraní PowerShell zařízení prostřednictvím protokolu HTTP. Ve výchozím nastavení je zapnutá Vzdálená správa. Zadejte heslo správce zařízení pro přihlášení na zařízení. Další informace najdete v části [připojit vzdáleně na vaše zařízení Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Následující osvědčené postupy mějte na paměti:

- Služba Data Box Edge nelze načíst existující hesla: ji můžete obnovit pouze prostřednictvím webu Azure portal. Doporučujeme, abyste tak, že není potřeba resetovat heslo, pokud je zapomenuté ukládání všech hesel na bezpečném místě. Pokud resetujete heslo, nezapomeňte informovat všechny uživatele před jeho resetování.
- Použití místní webové uživatelské rozhraní pro [změnit heslo](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, aby nesetkávají přihlášením.
- Rozhraní Windows PowerShell vašeho zařízení může přistupovat vzdáleně přes protokol HTTP. Z hlediska zabezpečení je nejvhodnější HTTP by měly používat jenom v důvěryhodných sítích.
- Ujistěte se, že hesla správce zařízení jsou silné a dobře chráněné. Postupujte podle [osvědčené postupy pro hesla](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení okraj pole dat, které chrání přenášená data a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

Pro na data-at-rest:

- Data Box Edge pro na data-at-rest, používá šifrování nástrojem BitLocker XTS AES-256 chránit místní data.
- Pro data uložená ve sdílených složkách je omezený přístup ke sdíleným složkám.

    - U klientů SMB přistupujících k datům sdílené složky musí přihlašovací údaje uživatele přidružené sdílené složky. Tyto přihlašovací údaje jsou definovány v době vytvoření sdílené složky.
    - IP adresy klientů pro klienty systému souborů NFS, které přistupují k sdíleným složkám nutné přidat v době vytvoření sdílené složky.


### <a name="protect-data-in-flight"></a>Chránit data na cestě

Pro data v let:

- Pro data, která přejdou mezi zařízením a Azure se používá standardní protokol TLS 1.2. Není záložní ověřování protokolu TLS 1.1 a dříve. Komunikace agenta se zablokuje, pokud není podporován protokol TLS 1.2. Také je vyžadována portál a operace správy SDK TLS 1.2.
- Pokud klienti přístup k vašemu zařízení prostřednictvím místního webového uživatelského rozhraní v prohlížeči, standardní TLS 1.2 slouží jako výchozí zabezpečený protokol.

    - Osvědčeným postupem je prohlížeč nakonfigurovat pro použití protokolu TLS 1.2.
    - Pokud prohlížeč nepodporuje protokol TLS 1.2, můžete použít protokol TLS 1.1 a TLS 1.0.
- K ochraně dat při kopírování z vašich dat serverů, doporučujeme použít se šifrování protokolu SMB 3.0.

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a úložiště dva 512bitové přístupové klíče přidružené k tomuto účtu úložiště.

Jeden z klíčů se používá k ověřování, když Data Box hraniční zařízení získá přístup k účtu úložiště. Další klíč se nachází ve fondu, abyste mohli klíče pravidelně otočit.

Z bezpečnostních důvodů se vyžadují mnoho datových centrech obměny klíče. Doporučujeme, abyste postupovali podle těchto osvědčených postupů pro obměna klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě Chraňte klíč účtu. Není distribuovat ostatním uživatelům heslo, intenzivně kód nebo ji uložit kdekoli ve formátu prostého textu, který je přístupný ostatním uživatelům.
- [Znovu vygenerovat klíč účtu](../storage/common/storage-account-manage.md#regenerate-access-keys) pomocí webu Azure portal, pokud si myslíte, že je možná ohrožené.
- Otočit o a poté [synchronizovat klíče účtu úložiště](data-box-gateway-manage-shares.md#sync-storage-keys) pravidelně k zajištění, že váš účet úložiště není přístup neoprávnění uživatelé.
- Správce Azure by měl pravidelně, změnit nebo znovu vygenerovat primární nebo sekundární klíč pomocí části úložiště na webu Azure portal pro přímý přístup k účtu úložiště.


## <a name="manage-personal-information"></a>Správa osobních údajů

Hraniční zařízení Data Box / služba Data Box brány shromažďuje osobní informace v následujících případech klíče:

- **Údaje objednávky** – po vytvoření objednávky se na webu Azure Portal uloží dodací adresa, e-mail a kontaktní informace uživatelů. Uložené údaje zahrnují:
  - Jméno kontaktu
  - Telefonní číslo
  - Email
  - Ulice a číslo
  - Město
  - PSČ
  - Stav
  - Provincie/kraj/oblast
  - Sledovací číslo zásilky

    Podrobnosti objednávky jsou zašifrované a uložené ve službě. Informace o službě uchová, dokud explicitně odstranit prostředek nebo pořadí. Kromě toho odstranění prostředku a odpovídající objednávka blokovaný od okamžiku, kdy zařízení se posílá, dokud se zařízení vrátí do Microsoftu.

- **Dodací adresa** – po se objednávka služby Data Box poskytuje k prostředníci třetích stran, jako je například UPS dodací adresu.

- **Sdílet uživatele** – na vašem zařízení mohou také uživatelé data uložená na sdílených složek na. Seznam uživatelů, kteří můžou přistupovat k datům sdílené složky se zobrazí a je možné zobrazit. Tento seznam se také odstraní při odstranění sdílené složky. Chcete-li zobrazit seznam uživatelů, kteří mohou přistupovat k nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na okraji pole Data](data-box-gateway-manage-shares.md).

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení Data Box Edge](data-box-edge-deploy-prep.md).

