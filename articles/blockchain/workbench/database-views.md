---
title: Zobrazení databáze v aplikaci Azure Blockchain Workbench
description: Přehled služby Azure Blockchain Workbench SQL DB zobrazení databáze.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4d5b98ab001bcb30091590880954c7075701e53b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607349"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Zobrazení databáze v aplikaci Azure Blockchain Workbench

Azure Blockchain Workbench dodává data z distribuované účetní knihy do *mimo řetězec* databáze SQL DB. To umožňuje pomocí SQL a stávající nástroje, jako například [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), pracovat s daty blockchain.

Azure Blockchain Workbench poskytuje sadu zobrazení databáze, které poskytují přístup k datům, která bude užitečná při provádění dotazů. Tato zobrazení jsou silně Nenormalizovaná snadno a rychle začít s vytvářením sestavy, analýzy, v opačném případě využívají data blockchain se stávajícími nástroji a bez nutnosti obnova databáze zaměstnanců.

Tato část obsahuje přehled o zobrazení databáze a dat, která obsahují.

> [!NOTE]
> Žádné přímé využití databázových tabulek v databázi mimo tato zobrazení při nejbližším se nepodporuje.
>

## <a name="vwapplication"></a>vwApplication

Toto zobrazení obsahuje podrobné informace o **aplikace** , které byly nahrány do Azure Blockchain Workbench.

| Název                             | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                  | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDescription           | nvarchar(255) | Ano         | Popis aplikace |
| ApplicationDisplayName           | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled               | Bit           | Ne          | Určuje, zda aplikace je aktuálně povoleno.<br /> **Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi. |
| UploadedDtTm                     | datetime2(7)  | Ne          | Datum a čas, který byl nahrán kontrakt |
| UploadedByUserId                 | int           | Ne          | ID uživatele, který nahráli aplikace |
| UploadedByUserExternalId         | nvarchar(255) | Ne          | Externí identifikátor pro uživatele, který nahráli aplikace. Ve výchozím nastavení to je ID uživatele z Azure Active Directory pro konsorcia.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Ne          | Určuje aktuální stav zřizování proces pro uživatele. Možné hodnoty: <br />0 – uživatel vytvořil rozhraní API<br />1 – klíč spojena s uživatelem v databázi<br />2 – uživatel je plně zřízený.                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele, který nahraje kontraktu |
| UploadedByUserLastName           | nvarchar(50)  | Ano         | Příjmení uživatele, který nahraje kontraktu |
| UploadedByUserEmailAddress       | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který nahraje kontraktu |

## <a name="vwapplicationrole"></a>vwApplicationRole

Toto zobrazení obsahuje podrobné informace o rolích, které byly definovány v aplikacích Azure Blockchain Workbench.

V *Asset přenosu* aplikace, například role, jako *kupujících* a *prodejce* role může být definovaná.

| Název                   | Typ             | Může mít hodnotu Null | Popis                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Ne          | Jedinečný identifikátor pro aplikaci           |
| ApplicationName        | nvarchar(50)     | Ne          | Název aplikace                       |
| ApplicationDescription | nvarchar(255)    | Ano         | Popis aplikace                  |
| ApplicationDisplayName | nvarchar(255)    | Ne          | Název, který se má zobrazit v uživatelském rozhraní      |
| RoleId                 | int              | Ne          | Jedinečný identifikátor pro roli v aplikaci |
| RoleName               | nvarchar50)      | Ne          | Název role                              |
| RoleDescription        | Description(255) | Ano         | Popis role                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Toto zobrazení obsahuje podrobné informace o rolích, které byly definovány v aplikacích Azure Blockchain Workbench a uživatelé k nim má přiřazené.

V *Asset přenos* aplikace, například *Jan Macek* může být přiřazena *kupujících* role.

| Název                       | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Ne          | Jedinečný identifikátor pro aplikaci                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Ne          | Název aplikace                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Ano         | Popis aplikace                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Ne          | Jedinečný identifikátor pro roli v aplikaci                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Ne          | Název role                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ano         | Popis role                                                                                                                                                                                                             |
| UserId                     | int           | Ne          | ID uživatele přidružené k roli |
| UserExternalId             | nvarchar(255) | Ne          | Externí identifikátor pro uživatele, který je přidružený k roli. Ve výchozím nastavení to je ID uživatele z Azure Active Directory pro konsorcia.                                                                     |
| UserProvisioningStatus     | int           | Ne          | Určuje aktuální stav zřizování proces pro uživatele. Možné hodnoty: <br />0 – uživatel vytvořil rozhraní API<br />1 – klíč spojena s uživatelem v databázi<br />2 – uživatel je plně zřízený. |
| UserFirstName              | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je přidružený k roli |
| UserLastName               | nvarchar(255) | Ano         | Příjmení uživatele, který je přidružený k roli |
| UserEmailAddress           | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který je přidružený k roli |

## <a name="vwconnectionuser"></a>vwConnectionUser

Toto zobrazení obsahuje podrobné informace o připojení definované v aplikaci Azure Blockchain Workbench a uživatelé k nim má přiřazené. Pro každé připojení toto zobrazení obsahuje následující data:

-   Podrobnosti přidružené knihy
-   Informace o přidružené uživateli

| Název                     | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID připojení             | int           | Ne          | Jedinečný identifikátor pro připojení v aplikaci Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Ne          | Adresa url koncového bodu připojení |
| ConnectionFundingAccount | nvarchar(255) | Ano         | Financování účet přidružený k připojení, pokud je k dispozici |
| LedgerId                 | int           | Ne          | Jedinečný identifikátor pro účetní kniha |
| LedgerName               | nvarchar(50)  | Ne          | Název knihy |
| LedgerDisplayName        | nvarchar(255) | Ne          | Název knihy, které chcete zobrazit v uživatelském rozhraní |
| UserId                   | int           | Ne          | ID uživatele přidružené k připojení |
| UserExternalId           | nvarchar(255) | Ne          | Externí identifikátor pro uživatele, který je přidružený k připojení. Ve výchozím nastavení to je ID uživatele z Azure Active Directory pro konsorcia. |
| UserProvisioningStatus   | int           | Ne          |Určuje aktuální stav zřizování proces pro uživatele. Možné hodnoty: <br />0 – uživatel vytvořil rozhraní API<br />1 – klíč spojena s uživatelem v databázi<br />2 – uživatel je plně zřízený. |
| UserFirstName            | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je přidružený k připojení |
| UserLastName             | nvarchar(255) | Ano         | Příjmení uživatele, který je přidružený k připojení |
| UserEmailAddress         | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který je přidružený k připojení |

## <a name="vwcontract"></a>vwContract

Toto zobrazení obsahuje podrobné informace o nasazených smluv. Pro každou smlouvu toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Implementace přidružené knihy pro funkci
-   Podrobnosti o uživateli, který inicioval akci
-   Podrobnosti týkající se blokování blockchain a transakce

| Název                                     | Typ           | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID připojení                             | int            | Ne          | Jedinečný identifikátor pro připojení v aplikaci Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Ne          | Adresa url koncového bodu připojení |
| ConnectionFundingAccount                 | nvarchar(255)  | Ano         | Financování účet přidružený k připojení, pokud je k dispozici |
| LedgerId                                 | int            | Ne          | Jedinečný identifikátor pro účetní kniha |
| LedgerName                               | nvarchar(50)   | Ne          | Název knihy |
| LedgerDisplayName                        | nvarchar(255)  | Ne          | Název knihy, které chcete zobrazit v uživatelském rozhraní |
| ApplicationId                            | int            | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                          | nvarchar (50)  | Ne          | Název aplikace |
| ApplicationDisplayName                   | nvarchar (255). | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | Bit            | Ne          | Určuje, zda je aplikace povolena.<br /> **Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi.  |
| WorkflowId                               | int            | Ne          | Jedinečný identifikátor pro pracovní postup spojený s kontraktem |
| WorkflowName                             | nvarchar(50)   | Ne          | Název pracovního postupu spojené s kontraktem |
| WorkflowDisplayName                      | nvarchar(255)  | Ne          | Název pracovního postupu přidružené ke smlouvě se zobrazují v uživatelském rozhraní |
| WorkflowDescription                      | nvarchar(255)  | Ano         | Popis pracovního postupu spojené s kontraktem |
| ContractCodeId                           | int            | Ne          | Jedinečný identifikátor pro kód smlouvy přidružené ke smlouvě |
| ContractFileName                         | int            | Ne          | Název souboru, který obsahuje kód inteligentní smlouvy pro tento pracovní postup. |
| ContractUploadedDtTm                     | int            | Ne          | Datum a čas, kdy byl odeslán kód kontraktu |
| ContractId                               | int            | Ne          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus               | int            | Ne          | Určuje aktuální stav procesu zřízení pro kontrakt. Možné hodnoty: <br />0 – smlouvy se vytvořila pomocí rozhraní API v databázi<br />1 – kontrakt byl odeslán na účetní knize<br />2 – Smlouvy se úspěšně nasadil do hlavní knihy<br />3 nebo 4 – smlouvy se dají nasadit pro hlavní knihy<br />5 – Smlouvy se úspěšně nasadilo do hlavní knihy <br /><br />Od verze 1.5, jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi, zobrazení **vwContractV0** je k dispozici, že podporuje pouze hodnoty 0 až 2. |
| ContractLedgerIdentifier                 | nvarchar (255). |             | E-mailovou adresu uživatele, který je nasazený kontraktu |
| ContractDeployedByUserId                 | int            | Ne          | Externí identifikátor pro uživatele, který je nasazený kontrakt. Ve výchozím nastavení toto je identifikátor guid představující ID Azure Active Directory pro uživatele.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Ne          | Externí identifikátor pro uživatele, který je nasazený kontrakt. Ve výchozím nastavení toto je identifikátor guid představující ID Azure Active Directory pro uživatele.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Ne          | Určuje aktuální stav procesu zřízení pro uživatele. Možné hodnoty: <br />0 – uživatel vytvořil rozhraní API<br />1 – klíč spojena s uživatelem v databázi <br />2 – uživatel je plně zřízený.                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Ano         | Křestní jméno uživatele, který je nasazený kontraktu |
| ContractDeployedByUserLastName           | nvarchar(255)  | Ano         | Příjmení uživatele, který je nasazený kontraktu |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Ano         | E-mailovou adresu uživatele, který je nasazený kontraktu |

## <a name="vwcontractaction"></a>vwContractAction

Toto zobrazení představuje většinu informací o související s akcí provedených u kontraktů a snadno usnadnit běžné scénáře vytváření sestav. Pro všechny akce podniknuté toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Kontrakt přidružený inteligentní funkce a parametr definice
-   Implementace přidružené knihy pro funkci
-   Konkrétní instance hodnoty poskytnutý pro parametry
-   Podrobnosti o uživateli, který inicioval akci
-   Podrobnosti týkající se blokování blockchain a transakce

| Název                                     | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                          | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName                   | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | Bit           | Ne          | Toto pole určuje, zda je aplikace povolena. Všimněte si – i když můžete projeví aplikace jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi.                                                  |
| WorkflowId                               | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                             | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName                      | nvarchar(255) | Ne          | Název pracovního postupu, který se zobrazí v uživatelském rozhraní |
| WorkflowDescription                      | nvarchar(255) | Ano         | Popis pracovního postupu |
| ContractId                               | int           | Ne          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus               | int           | Ne          | Určuje aktuální stav procesu zřízení pro kontrakt. Možné hodnoty: <br />0 – smlouvy se vytvořila pomocí rozhraní API v databázi<br />1 – kontrakt byl odeslán na účetní knize<br />2 – Smlouvy se úspěšně nasadil do hlavní knihy<br />3 nebo 4 – smlouvy se dají nasadit pro hlavní knihy<br />5 – Smlouvy se úspěšně nasadilo do hlavní knihy <br /><br />Od verze 1.5, jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi, zobrazení **vwContractActionV0** je k dispozici, že podporuje pouze hodnoty 0 až 2. |
| ContractCodeId                           | int           | Ne          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractLedgerIdentifier                 | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuované účetní knihy. Například Etherea. |
| ContractDeployedByUserId                 | int           | Ne          | Jedinečný identifikátor uživatele, kterého je nasazený kontraktu |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je nasazený kontraktu |
| ContractDeployedByUserLastName           | nvarchar(255) | Ano         | Příjmení uživatele, který je nasazený kontraktu |
| ContractDeployedByUserExternalId         | nvarchar(255) | Ne          | Externí identifikátor uživatele, který je nasazený kontrakt. Ve výchozím nastavení to je identifikátor guid, který představuje identity v Azure Active Directory consortium.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který je nasazený kontraktu |
| WorkflowFunctionId                       | int           | Ne          | Jedinečný identifikátor pro funkce workflowu |
| WorkflowFunctionName                     | nvarchar(50)  | Ne          | Název funkce |
| WorkflowFunctionDisplayName              | nvarchar(255) | Ne          | Název funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionDescription              | nvarchar(255) | Ne          | Popis funkce |
| ContractActionId                         | int           | Ne          | Jedinečný identifikátor pro akci kontraktu |
| ContractActionProvisioningStatus         | int           | Ne          | Určuje aktuální stav procesu zřízení pro akce kontraktu. Možné hodnoty: <br />0 – akce smlouvy se vytvořila pomocí rozhraní API v databázi<br />1 – akce smlouvy se poslal na hlavní knihy<br />2 – akce smlouvy se úspěšně nasadil do hlavní knihy<br />3 nebo 4 – smlouvy se dají nasadit pro hlavní knihy<br />5 – Smlouvy se úspěšně nasadilo do hlavní knihy <br /><br />Od verze 1.5, jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi, zobrazení **vwContractActionV0** je k dispozici, že podporuje pouze hodnoty 0 až 2. |
| ContractActionTimestamp                  | DATETIME(2,7) | Ne          | Časové razítko akce kontraktu |
| ContractActionExecutedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, který spouští akce kontraktu |
| ContractActionExecutedByUserFirstName    | int           | Ano         | Křestní jméno uživatele, který provedl akci kontraktu |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Ano         | Příjmení uživatele, který provedl akci kontraktu |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Ano         | Externí identifikátor uživatele, který provedl akci kontraktu. Ve výchozím nastavení to je identifikátor guid, který představuje identity v Azure Active Directory consortium. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který provedl akci kontraktu |
| WorkflowFunctionParameterId              | int           | Ne          | Jedinečný identifikátor pro parametr funkce |
| WorkflowFunctionParameterName            | nvarchar(50)  | Ne          | Název parametru funkce |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Ne          | Název parametru funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionParameterDataTypeId      | int           | Ne          | Jedinečný identifikátor pro datového typu asociované s parametrem funkce pracovního postupu |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Ne          | Název datového typu asociované s parametrem funkce pracovního postupu |
| ContractActionParameterValue             | nvarchar(255) | Ne          | Hodnota parametru uložené v inteligentní kontraktu |
| BlockHash                                | nvarchar(255) | Ano         | Hodnota hash bloku |
| BlockNumber                              | int           | Ano         | Počet blok na hlavní knihy |
| BlockTimestamp                           | DATETIME(2,7) | Ano         | Časové razítko bloku |
| Identifikátor TransactionId                            | int           | Ne          | Jedinečný identifikátor pro transakce |
| TransactionFrom                          | nvarchar(255) | Ano         | Strana, která pochází transakce |
| TransactionTo                            | nvarchar(255) | Ano         | Strana, která byla provedena s transakce |
| TransactionHash                          | nvarchar(255) | Ano         | Hodnota hash transakce |
| TransactionIsWorkbenchTransaction        | Bit           | Ano         | Bit, který určuje, zda transakce je transakce Azure Blockchain Workbench |
| TransactionProvisioningStatus            | int           | Ano         | Určuje aktuální stav procesu zřízení pro transakce. Možné hodnoty: <br />0 – transakce byla vytvořena pomocí rozhraní API v databázi<br />1 – transakce byl odeslán na účetní knize<br />2 – transakce byla úspěšně nasazena do hlavní knihy                 |
| TransactionValue                         | Decimal(32,2) | Ano         | Částka transakce |

## <a name="vwcontractproperty"></a>vwContractProperty

Toto zobrazení představuje většinu informací o související vlastnosti související s kontraktem a snadno usnadnit běžné scénáře vytváření sestav. Pro každou vlastnost přijata toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Podrobnosti o uživateli, který nasazení pracovního postupu
-   Definice vlastnosti přidružené inteligentní kontraktu
-   Konkrétní instance hodnoty vlastností
-   Podrobnosti o stavu vlastnost kontraktu

| Název                               | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                    | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName             | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | Bit           | Ne          | Určuje, zda je aplikace povolena.<br />**Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi.                      |
| WorkflowId                         | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                       | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName                | nvarchar(255) | Ne          | Název pracovního postupu, zobrazí v uživatelském rozhraní |
| WorkflowDescription                | nvarchar(255) | Ano         | Popis pracovního postupu |
| ContractId                         | int           | Ne          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus         | int           | Ne          | Určuje aktuální stav procesu zřízení pro kontrakt. Možné hodnoty: <br />0 – smlouvy se vytvořila pomocí rozhraní API v databázi<br />1 – kontrakt byl odeslán na účetní knize<br />2 – Smlouvy se úspěšně nasadil do hlavní knihy<br />3 nebo 4 – smlouvy se dají nasadit pro hlavní knihy<br />5 – Smlouvy se úspěšně nasadilo do hlavní knihy <br /><br />Od verze 1.5, jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi, zobrazení **vwContractPropertyV0** je k dispozici, že podporuje pouze hodnoty 0 až 2. |
| ContractCodeId                     | int           | Ne          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractLedgerIdentifier           | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuované účetní knihy. Například Etherea. |
| ContractDeployedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, kterého je nasazený kontraktu |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je nasazený kontraktu |
| ContractDeployedByUserLastName     | nvarchar(255) | Ano         | Příjmení uživatele, který je nasazený kontraktu |
| ContractDeployedByUserExternalId   | nvarchar(255) | Ne          | Externí identifikátor uživatele, který je nasazený kontrakt. Ve výchozím nastavení to je identifikátor guid, který představuje svoji identitu v consortium Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který je nasazený kontraktu |
| WorkflowPropertyId                 | int           |             | Jedinečný identifikátor vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | Ne          | ID datového typu vlastnosti |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Ne          | Název datového typu vlastnosti |
| WorkflowPropertyName               | nvarchar(50)  | Ne          | Název vlastnosti pracovního postupu |
| WorkflowPropertyDisplayName        | nvarchar(255) | Ne          | Zobrazovaný název vlastnosti pracovního postupu |
| WorkflowPropertyDescription        | nvarchar(255) | Ano         | Popis vlastnosti |
| ContractPropertyValue              | nvarchar(255) | Ne          | Hodnota vlastnosti kontraktu |
| StateName                          | nvarchar(50)  | Ano         | Pokud tato vlastnost obsahuje informace o stavu kontraktu, toto je zobrazovaný název pro stav. Pokud není související se stavem, hodnota bude mít hodnotu null. |
| StateDisplayName                   | nvarchar(255) | Ne          | Pokud tato vlastnost obsahuje informace o stavu, toto je zobrazovaný název pro stav. Pokud není související se stavem, hodnota bude mít hodnotu null. |
| StateValue                         | nvarchar(255) | Ano         | Pokud tato vlastnost obsahuje informace o stavu, jedná se o hodnotu stavu. Pokud není související se stavem, hodnota bude mít hodnotu null. |

## <a name="vwcontractstate"></a>vwContractState

Toto zobrazení představuje většinu informací týkajících se stavu konkrétní kontrakt a snadno usnadnit běžné scénáře vytváření sestav. Každý záznam v tomto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Podrobnosti o uživateli, který nasazení pracovního postupu
-   Definice vlastnosti přidružené inteligentní kontraktu
-   Podrobnosti o stavu vlastnost kontraktu

| Název                               | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                    | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName             | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | Bit           | Ne          | Určuje, zda je aplikace povolena.<br />**Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi. |
| WorkflowId                         | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                       | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName                | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní |
| WorkflowDescription                | nvarchar(255) | Ano         | Popis pracovního postupu |
| ContractLedgerImplementationId     | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuované účetní knihy. Například Etherea. |
| ContractId                         | int           | Ne          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus         | int           | Ne          |Určuje aktuální stav procesu zřízení pro kontrakt. Možné hodnoty: <br />0 – smlouvy se vytvořila pomocí rozhraní API v databázi<br />1 – kontrakt byl odeslán na účetní knize<br />2 – Smlouvy se úspěšně nasadil do hlavní knihy<br />3 nebo 4 – smlouvy se dají nasadit pro hlavní knihy<br />5 – Smlouvy se úspěšně nasadilo do hlavní knihy <br /><br />Od verze 1.5, jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi, zobrazení **vwContractStateV0** je k dispozici, že podporuje pouze hodnoty 0 až 2. |
| ID připojení                       | int           | Ne          | Jedinečný identifikátor instance pracovního postupu blockchain nasazuje do |
| ContractCodeId                     | int           | Ne          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractDeployedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, kterého je nasazený kontraktu |
| ContractDeployedByUserExternalId   | nvarchar(255) | Ne          | Externí identifikátor uživatele, který je nasazený kontrakt. Ve výchozím nastavení to je identifikátor guid, který představuje identity v Azure Active Directory consortium. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je nasazený kontraktu |
| ContractDeployedByUserLastName     | nvarchar(255) | Ano         | Příjmení uživatele, který je nasazený kontraktu |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ano         | E-mailovou adresu uživatele, který je nasazený kontraktu |
| WorkflowPropertyId                 | int           | Ne          | Jedinečný identifikátor vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | Ne          | ID datového typu vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Ne          | Název datového typu vlastnosti pracovního postupu |
| WorkflowPropertyName               | nvarchar(50)  | Ne          | Název vlastnosti pracovního postupu |
| WorkflowPropertyDisplayName        | nvarchar(255) | Ne          | Zobrazovaný název vlastnosti, která se zobrazí v uživatelském rozhraní |
| WorkflowPropertyDescription        | nvarchar(255) | Ano         | Popis vlastnosti |
| ContractPropertyValue              | nvarchar(255) | Ne          | Hodnota vlastnosti uložené v kontraktu |
| StateName                          | nvarchar(50)  | Ano         | Pokud tato vlastnost obsahuje informace o stavu, toto je zobrazovaný název pro stav. Pokud není související se stavem, hodnota bude mít hodnotu null. |
| StateDisplayName                   | nvarchar(255) | Ne          | Pokud tato vlastnost obsahuje informace o stavu, toto je zobrazovaný název pro stav. Pokud není související se stavem, hodnota bude mít hodnotu null. |
| StateValue                         | nvarchar(255) | Ano         | Pokud tato vlastnost obsahuje informace o stavu, jedná se o hodnotu stavu. Pokud není související se stavem, hodnota bude mít hodnotu null. |

## <a name="vwuser"></a>vwUser

Toto zobrazení obsahuje podrobné informace na consortium členů, které je zajištěno jeho použití pro Azure Blockchain Workbench. Ve výchozím nastavení se vyplní data prostřednictvím počátečního zřízení uživatele.

| Název               | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Ne          | Jedinečný identifikátor uživatele |
| externalID         | nvarchar(255) | Ne          | Externí identifikátor pro uživatele. Ve výchozím nastavení toto je identifikátor guid představující ID Azure Active Directory pro uživatele. |
| ProvisioningStatus | int           | Ne          |Určuje aktuální stav zřizování proces pro uživatele. Možné hodnoty: <br />0 – uživatel vytvořil rozhraní API<br />1 – klíč spojena s uživatelem v databázi<br />2 – uživatel je plně zřízený. |
| FirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele |
| LastName           | nvarchar(50)  | Ano         | Příjmení uživatele |
| EmailAddress       | nvarchar(255) | Ano         | E-mailovou adresu uživatele |

## <a name="vwworkflow"></a>vwWorkflow

Toto zobrazení představuje podrobnosti základní pracovní postup metadata a také funkce a parametry pracovního postupu. Je určená pro vytváření sestav a také obsahuje metadata aplikace spojené s pracovním postupem. Toto zobrazení obsahuje data z více podkladových tabulek pro usnadnění vytváření sestav o pracovních postupů. Pro každý pracovní postup toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Související informace o stavu spuštění pracovního postupu

| Název                              | Typ          | Může mít hodnotu Null | Popis                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                   | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName            | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                | Bit           | Ne          | Určuje, zda je aplikace povolena |
| WorkflowId                        | int           | Ano         | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                      | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName               | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní |
| WorkflowDescription               | nvarchar(255) | Ano         | Popis pracovního postupu. |
| WorkflowConstructorFunctionId     | int           | Ne          | Identifikátor funkce workflowu, který slouží jako konstruktor pro pracovní postup |
| WorkflowStartStateId              | int           | Ne          | Jedinečný identifikátor stavu |
| WorkflowStartStateName            | nvarchar(50)  | Ne          | Název stavu |
| WorkflowStartStateDisplayName     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStartStateDescription     | nvarchar(255) | Ano         | Popis stavu pracovního postupu |
| WorkflowStartStateStyle           | nvarchar(50)  | Ano         | Tato hodnota určuje procento dokončení, že pracovní postup je v tomto stavu |
| WorkflowStartStateValue           | int           | Ne          | Toto je hodnota stavu |
| WorkflowStartStatePercentComplete | int           | Ne          | Textový popis, který poskytuje nápovědu pro klienty o tom, jak vykreslit tento stav v uživatelském rozhraní. Podporované stavy jsou *úspěch* a *selhání* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Toto zobrazení představuje podrobnosti základní pracovní postup metadata a také funkce a parametry pracovního postupu. Je určená pro vytváření sestav a také obsahuje metadata aplikace spojené s pracovním postupem. Toto zobrazení obsahuje data z více podkladových tabulek pro usnadnění vytváření sestav o pracovních postupů. Pro každou funkci pracovního postupu toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Podrobnosti o funkci pracovního postupu

| Název                                 | Typ          | Může mít hodnotu Null | Popis                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName                      | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName               | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                   | Bit           | Ne          | Určuje, zda je aplikace povolena |
| WorkflowId                           | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                         | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName                  | nvarchar(255) | Ne          | Název pracovního postupu, zobrazí v uživatelském rozhraní |
| WorkflowDescription                  | nvarchar(255) | Ano         | Popis pracovního postupu |
| WorkflowFunctionId                   | int           | Ne          | Jedinečný identifikátor pro funkci |
| WorkflowFunctionName                 | nvarchar(50)  | Ano         | Název funkce |
| WorkflowFunctionDisplayName          | nvarchar(255) | Ne          | Název funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionDescription          | nvarchar(255) | Ano         | Popis funkce workflowu |
| WorkflowFunctionIsConstructor        | Bit           | Ne          | Určuje, pokud je pracovní postup funkce konstruktoru pro pracovní postup |
| WorkflowFunctionParameterId          | int           | Ne          | Jedinečný identifikátor pro parametr funkce |
| WorkflowFunctionParameterName        | nvarchar(50)  | Ne          | Název parametru funkce |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Ne          | Název parametru funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionParameterDataTypeId  | int           | Ne          | Jedinečný identifikátor pro datového typu asociované s parametrem funkce pracovního postupu |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Ne          | Název datového typu asociované s parametrem funkce pracovního postupu |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Toto zobrazení představuje vlastností definovaných pro pracovní postup. Pro každou vlastnost toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Podrobnosti vlastnosti pracovního postupu

| Název                         | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName              | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName       | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled           | Bit           | Ne          | Určuje, zda je aplikace povolena.<br />**Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi. |
| WorkflowId                   | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                 | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName          | nvarchar(255) | Ne          | Název, který má být zobrazen pro pracovní postup v uživatelském rozhraní |
| WorkflowDescription          | nvarchar(255) | Ano         | Popis pracovního postupu |
| WorkflowPropertyID           | int           | Ne          | Jedinečný identifikátor vlastnosti pracovního postupu |
| WorkflowPropertyName         | nvarchar(50)  | Ne          | Název vlastnosti |
| WorkflowPropertyDescription  | nvarchar(255) | Ano         | Popis vlastnosti |
| WorkflowPropertyDisplayName  | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| WorkflowPropertyWorkflowId   | int           | Ne          | ID pracovního postupu, ke kterému je tato vlastnost přidružená |
| WorkflowPropertyDataTypeId   | int           | Ne          | ID datového typu definované pro vlastnost |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Ne          | Název datového typu definované pro vlastnost |
| WorkflowPropertyIsState      | Bit           | Ne          | Toto pole určuje, zda tato vlastnost pracovního postupu obsahuje stavu pracovního postupu |

## <a name="vwworkflowstate"></a>vwWorkflowState

Toto zobrazení znázorňuje vlastnosti související s pracovním postupem. Pro každou smlouvu toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Definice pracovního postupu přidružená
-   Informace o stavu pracovního postupu

| Název                         | Typ          | Může mít hodnotu Null | Popis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Ne          | Jedinečný identifikátor pro aplikaci |
| ApplicationName              | nvarchar(50)  | Ne          | Název aplikace |
| ApplicationDisplayName       | nvarchar(255) | Ne          | Popis aplikace |
| ApplicationEnabled           | Bit           | Ne          | Určuje, zda je aplikace povolena.<br />**Poznámka:** I v případě, že aplikace může projeví jako zakázané v databázi, související smlouvy zůstávají v blockchainu a data o těchto smluv zůstat v databázi. |
| WorkflowId                   | int           | Ne          | Jedinečný identifikátor pro pracovní postup |
| WorkflowName                 | nvarchar(50)  | Ne          | Název pracovního postupu |
| WorkflowDisplayName          | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní pro pracovní postup |
| WorkflowDescription          | nvarchar(255) | Ano         | Popis pracovního postupu |
| WorkflowStateID              | int           | Ne          | Jedinečný identifikátor stavu |
| WorkflowStateName            | nvarchar(50)  | Ne          | Název stavu |
| WorkflowStateDisplayName     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStateDescription     | nvarchar(255) | Ano         | Popis stavu pracovního postupu |
| WorkflowStatePercentComplete | int           | Ne          | Tato hodnota určuje procento dokončení, že pracovní postup je v tomto stavu |
| WorkflowStateValue           | nvarchar(50)  | Ne          | Toto je hodnota stavu |
| WorkflowStateStyle           | nvarchar(50)  | Ne          | Textový popis, který poskytuje nápovědu pro klienty o tom, jak vykreslit tento stav v uživatelském rozhraní. Podporované stavy jsou *úspěch* a *selhání* |
