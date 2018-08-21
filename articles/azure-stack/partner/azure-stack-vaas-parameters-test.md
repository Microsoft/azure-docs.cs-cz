---
title: Testovací parametry pro ověření jako službu Azure Stack | Dokumentace Microsoftu
description: Téma referenčních informací o konfigurační soubor a testu předávat parametry pro ověření jako službu Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234922"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Parametry testu pro ověření jako službu Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Než spustíte všechny testovací sady z ověření jako služba (VaaS), vyberte řešení a vytvořte průchodu testu.

- Přihlaste se pomocí registrovaného VaaS přihlašovacích údajů klienta.
- Vytvořit nové řešení (tak, že vyberete **přidat řešení**) nebo vyberte všechny existující.
- Vyberte **Start** tlačítko **úspěšných testů** dlaždice pracovního postupu.

> [!Note]  
> Vytvořte novou položku řešení pro každý jedinečných počítačů sada/hardwarová konfigurace, který jste ověřování a nového testu předat pro každé nasazení sestavení v dané sadě počítačů.

Budete muset zadat parametry požadované pro spuštění testů **testu předat informace** stránky. Při spuštění nového průchodu testů nebo spuštění ověřování, zadejte tyto parametry. Většina parametrů mají stejné hodnoty, které jste zadali při nasazení Azure Stack.

Můžete zadat hodnoty ručně uvedené v [testovací parametry tabulky](#test-parameters), nebo nahrát konfigurační soubor nasazení, který obsahuje úplné informace razítko Azure Stack. Po nahrání na portál načítá hodnoty ze souboru.

> [!Note]  
> Můžete vyhledat a zadáte hodnoty parametrů testu tak, že vyhledání a načtení konfiguračního souboru do portálu.

## <a name="export-the-test-parameters-using-powershell"></a>Export parametry testu pomocí Powershellu

1. Přihlaste se k počítači DVM nebo jakýkoli počítač, který má přístup k prostředí Azure Stack.
2. Otevřete okno Powershellu se zvýšenými oprávněními a spusťte:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Nahrát **stampinfoproperties.json** VaaS portál.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>V konfiguračním souboru najít parametry testu

Můžete také vyhledat testovací hodnoty parametrů tak, že otevřete OSN **konfigurační soubor**. Můžete najít soubor v následujícím umístění na počítači DVM:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Parametry testu 

| Parametr    | Popis |
|-------------|-----------------|
| Sestavení služby Azure Stack                      | Azure Stack sestavení ani číslo verze, která byla nasazena například 1.0.170330.0 | 
| ID tenanta                              | Azure Active Directory Tenanta zadávají během nasazení Azure Stack. Vyhledejte **AADTenant** konfigurační soubor a vyberte **GUID** hodnotu `Id` značky. | 
| Oblast                                 | Oblast nasazení Azure Stack. Vyhledejte **oblasti** v konfiguračním souboru. | 
| Správce prostředků tenanta                | Koncový bod Azure Resource Manageru, třeba tenanta `https://management.<ExternalFqdn>` | 
| Správce Resource Manageru                 | Koncový bod Azure Resource Manageru správce. Například `https://adminmanagement.<ExternalFqdn>` | 
| Externí plně kvalifikovaný název domény                          | Externí plně kvalifikovaný název domény prostředí. Vyhledejte **ExternalFqdn** v konfiguračním souboru. | 
| Uživatel tenanta                            | Azure správce Tenanta Active Directory, které bylo zřízené již nebo musí být zřízené služby správce v adresáři Azure AD. Viz podrobnosti o zřizování účtu tenanta [přidat nový účet tenanta služby Azure Stack v Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Tato hodnota se používá testu k provedení operace na úrovni tenanta jako je nasazení šablony zřizovat prostředky (Virtuálního počítače, úložiště účtů atd) a spouštět úlohy. | 
| Uživatel s oprávněním správce služby             | Azure Active Directory správce tenanta Azure AD Directory zadávají během nasazení Azure Stack. Vyhledejte **AADTenant** v konfiguraci souboru a vyberte hodnotu v `UniqueName` značky v konfiguračním souboru. | 

## <a name="checks-before-starting-the-tests"></a>Kontroly před zahájením testů

Testy provádět vzdálené operace. Počítač, který spouští testy musí mít přístup ke koncovým bodům služby Azure Stack. Jinak test nebude fungovat. Pokud používáte VaaS na místní agent, využívat počítač, ve kterém bude agent spuštěn. Můžete ověřit, že váš počítač má přístup k bodům služby Azure Stack spuštěním následující kontroly.

1. Zkontrolujte, jestli se dá kontaktovat základní identifikátor URI. Otevřete příkazový řádek nebo prostředí bash a spusťte následující příkaz:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otevřete webový prohlížeč a přejděte do `https://adminportal.<EXTERNALFQDN>` za účelem ověření, jestli se dá kontaktovat portál MAS.

3. Přihlášení pomocí Azure AD služby hodnoty název a heslo správce určili při vytváření průchodu testu.

## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
