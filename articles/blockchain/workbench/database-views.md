---
title: Zobrazení databáze Azure blockchain Workbench
description: Přehled dostupných zobrazení databáze SQL DB ve službě Azure blockchain Workbench Preview
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e715bd2297c1e051ae92b15d73c945c9e80c3d94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87003317"
---
# <a name="azure-blockchain-workbench-database-views"></a>Zobrazení databáze Azure blockchain Workbench

Azure blockchain Workbench Preview doručuje data z distribuovaných knih do databáze SQL DB *mimo řetěz* . Databáze s neplatnými možnostmi umožňuje používat SQL a stávající nástroje, jako je [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), k interakci s daty blockchain.

Azure blockchain Workbench poskytuje sadu databázových zobrazení, která poskytují přístup k datům, která budou užitečná při provádění dotazů. Tato zobrazení jsou silně Denormalizovaná, aby bylo snadné rychle začít sestavovat sestavy, analyzovat a jinak využívat blockchain data pomocí stávajících nástrojů a bez nutnosti předávat zdroje databáze.

Tato část obsahuje přehled zobrazení databáze a data, která obsahují.

> [!NOTE]
> Jakékoli přímé použití databázových tabulek, které jsou v databázi mimo tato zobrazení k dispozici, ale je možné, není podporováno.
>

## <a name="vwapplication"></a>vwApplication

Toto zobrazení obsahuje podrobné informace o **aplikacích** , které se nahrály do Azure blockchain Workbench.

| Název                             | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                  | nvarchar (50)  | No          | Název aplikace |
| ApplicationDescription           | nvarchar (255) | Yes         | Popis aplikace |
| ApplicationDisplayName           | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled               | bit           | No          | Určuje, jestli je aplikace momentálně povolená.<br /> **Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi. |
| UploadedDtTm                     | datetime2 (7)  | No          | Datum a čas odeslání smlouvy |
| UploadedByUserId                 | int           | No          | ID uživatele, který aplikaci nahrál |
| UploadedByUserExternalId         | nvarchar (255) | No          | Externí identifikátor pro uživatele, který aplikaci nahrál. Ve výchozím nastavení je toto ID uživatel z Azure Active Directory pro konsorcium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | No          | Určuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API.<br />1 – klíč byl přidružen k uživateli v databázi.<br />2 – uživatel je plně zřízený.                         |
| UploadedByUserFirstName          | nvarchar (50)  | Yes         | Křestní jméno uživatele, který nahrál kontrakt |
| UploadedByUserLastName           | nvarchar (50)  | Yes         | Příjmení uživatele, který nahrál kontrakt |
| UploadedByUserEmailAddress       | nvarchar (255) | Yes         | E-mailová adresa uživatele, který nahrál kontrakt |

## <a name="vwapplicationrole"></a>vwApplicationRole

Toto zobrazení obsahuje podrobné informace o rolích, které jsou definované v aplikacích Azure blockchain Workbench.

V aplikaci pro *přenos assetu* se můžou definovat například role *kupující* a *prodejce* .

| Název                   | Typ             | Může mít hodnotu null. | Description                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | No          | Jedinečný identifikátor aplikace           |
| ApplicationName        | nvarchar (50)     | No          | Název aplikace                       |
| ApplicationDescription | nvarchar (255)    | Yes         | Popis aplikace                  |
| ApplicationDisplayName | nvarchar (255)    | No          | Název, který se má zobrazit v uživatelském rozhraní      |
| RoleId                 | int              | No          | Jedinečný identifikátor role v aplikaci |
| RoleName (Název role)               | nvarchar50)      | No          | Název role                              |
| RoleDescription        | Popis (255) | Yes         | Popis role                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Toto zobrazení obsahuje podrobné informace o rolích definovaných v aplikacích Azure blockchain Workbench a uživatelích s nimi spojených.

V aplikaci pro *přenos assetů* může být například *Jan Novák* přidružen k roli *Buyer* .

| Název                       | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | No          | Jedinečný identifikátor aplikace                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | No          | Název aplikace                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Yes         | Popis aplikace                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní                                                                                                                                                                                          |
| ApplicationRoleId          | int           | No          | Jedinečný identifikátor role v aplikaci                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | No          | Název role                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Yes         | Popis role                                                                                                                                                                                                             |
| UserId                     | int           | No          | ID uživatele přidruženého k roli |
| UserExternalId             | nvarchar (255) | No          | Externí identifikátor uživatele, který je přidružen k této roli. Ve výchozím nastavení je toto ID uživatel z Azure Active Directory pro konsorcium.                                                                     |
| UserProvisioningStatus     | int           | No          | Určuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API.<br />1 – klíč byl přidružen k uživateli v databázi.<br />2 – uživatel je plně zřízený. |
| UserFirstName              | nvarchar (50)  | Yes         | Křestní jméno uživatele přidruženého k roli |
| UserLastName               | nvarchar (255) | Yes         | Příjmení uživatele, který je přidružený k roli |
| UserEmailAddress           | nvarchar (255) | Yes         | E-mailová adresa uživatele, který je přidružen k roli |

## <a name="vwconnectionuser"></a>vwConnectionUser

Toto zobrazení poskytuje podrobné informace o připojeních definovaných v Azure blockchain Workbench a uživatelích s nimi spojených. Pro každé připojení obsahuje toto zobrazení následující data:

-   Podrobnosti o přidružené knize
-   Přidružené informace o uživateli

| Název                     | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | No          | Jedinečný identifikátor připojení v Azure blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar (50)  | No          | Adresa URL koncového bodu pro připojení |
| ConnectionFundingAccount | nvarchar (255) | Yes         | Účet financování přidružený k připojení, pokud je k dispozici |
| LedgerId                 | int           | No          | Jedinečný identifikátor pro hlavní knihu |
| Hlavní kniha               | nvarchar (50)  | No          | Název hlavní knihy |
| LedgerDisplayName        | nvarchar (255) | No          | Název hlavní knihy, která se má zobrazit v uživatelském rozhraní |
| UserId                   | int           | No          | ID uživatele přidruženého k připojení |
| UserExternalId           | nvarchar (255) | No          | Externí identifikátor uživatele, který je přidružen k připojení. Ve výchozím nastavení je toto ID uživatel z Azure Active Directory pro konsorcium. |
| UserProvisioningStatus   | int           | No          |Určuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API.<br />1 – klíč byl přidružen k uživateli v databázi.<br />2 – uživatel je plně zřízený. |
| UserFirstName            | nvarchar (50)  | Yes         | Křestní jméno uživatele, který je přidružen k připojení |
| UserLastName             | nvarchar (255) | Yes         | Příjmení uživatele, který je přidružen k připojení |
| UserEmailAddress         | nvarchar (255) | Yes         | E-mailová adresa uživatele, který je přidružen k připojení |

## <a name="vwcontract"></a>vwContract

Toto zobrazení poskytuje podrobné informace o nasazených smlouvách. Pro každou kontrakt obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Přidružená implementace hlavní knihy pro funkci
-   Podrobnosti o uživateli, který spustil akci
-   Podrobnosti související s blokem blockchain a transakcí

| Název                                     | Typ           | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | No          | Jedinečný identifikátor připojení v Azure blockchain Workbench                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | No          | Adresa URL koncového bodu pro připojení |
| ConnectionFundingAccount                 | nvarchar (255)  | Yes         | Účet financování přidružený k připojení, pokud je k dispozici |
| LedgerId                                 | int            | No          | Jedinečný identifikátor pro hlavní knihu |
| Hlavní kniha                               | nvarchar (50)   | No          | Název hlavní knihy |
| LedgerDisplayName                        | nvarchar (255)  | No          | Název hlavní knihy, která se má zobrazit v uživatelském rozhraní |
| ApplicationId                            | int            | No          | Jedinečný identifikátor aplikace |
| ApplicationName                          | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName                   | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | bit            | No          | Určuje, jestli je aplikace momentálně povolená.<br /> **Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi.  |
| WorkflowId                               | int            | No          | Jedinečný identifikátor pracovního postupu přidruženého ke kontraktu |
| WorkflowName                             | nvarchar (50)   | No          | Název pracovního postupu přidruženého ke kontraktu |
| WorkflowDisplayName                      | nvarchar (255)  | No          | Název pracovního postupu přidruženého ke kontraktu zobrazenému v uživatelském rozhraní |
| WorkflowDescription                      | nvarchar (255)  | Yes         | Popis pracovního postupu přidruženého ke kontraktu |
| ContractCodeId                           | int            | No          | Jedinečný identifikátor kódu kontraktu přidruženého ke smlouvě |
| ContractFileName                         | int            | No          | Název souboru, který obsahuje kód inteligentní smlouvy pro tento pracovní postup. |
| ContractUploadedDtTm                     | int            | No          | Datum a čas odeslání kódu kontraktu |
| ContractId                               | int            | No          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus               | int            | No          | Určuje aktuální stav procesu zřizování pro kontrakt. Možné hodnoty: <br />0 – kontrakt byl vytvořen rozhraním API v databázi.<br />1 – kontrakt byl odeslán do hlavní knihy.<br />2 – smlouva se úspěšně nasadila do hlavní knihy.<br />3 nebo 4 – smlouvu se nepovedlo nasadit do hlavní knihy.<br />5 – smlouva se úspěšně nasadila do hlavní knihy. <br /><br />Od verze 1,5 jsou podporovány hodnoty od 0 do 5. V případě zpětné kompatibility v aktuální verzi je dostupná hodnota zobrazení **vwContractV0** , která podporuje pouze hodnoty 0 až 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | E-mailová adresa uživatele, který smlouvu nasadil |
| ContractDeployedByUserId                 | int            | No          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje ID Azure Active Directory pro uživatele.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | No          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje ID Azure Active Directory pro uživatele.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | No          | Určuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API.<br />1 – klíč byl přidružen k uživateli v databázi. <br />2 – uživatel je plně zřízený.                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Yes         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName           | nvarchar (255)  | Yes         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Yes         | E-mailová adresa uživatele, který smlouvu nasadil |

## <a name="vwcontractaction"></a>vwContractAction

Toto zobrazení představuje většinu informací souvisejících s akcemi provedenými u kontraktů a je navržena tak, aby bylo možné snadno usnadnit běžné scénáře vytváření sestav. Pro každou provedenou akci obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Přidružená funkce a definice parametru inteligentního kontraktu
-   Přidružená implementace hlavní knihy pro funkci
-   Konkrétní hodnoty instance zadané pro parametry
-   Podrobnosti o uživateli, který spustil akci
-   Podrobnosti související s blokem blockchain a transakcí

| Název                                     | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                          | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName                   | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | bit           | No          | Toto pole označuje, jestli je aplikace momentálně povolená. Poznámka – i když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi.                                                  |
| WorkflowId                               | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                             | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName                      | nvarchar (255) | No          | Název pracovního postupu, který se má zobrazit v uživatelském rozhraní |
| WorkflowDescription                      | nvarchar (255) | Yes         | Popis pracovního postupu |
| ContractId                               | int           | No          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus               | int           | No          | Určuje aktuální stav procesu zřizování pro kontrakt. Možné hodnoty: <br />0 – kontrakt byl vytvořen rozhraním API v databázi.<br />1 – kontrakt byl odeslán do hlavní knihy.<br />2 – smlouva se úspěšně nasadila do hlavní knihy.<br />3 nebo 4 – smlouvu se nepovedlo nasadit do hlavní knihy.<br />5 – smlouva se úspěšně nasadila do hlavní knihy. <br /><br />Od verze 1,5 jsou podporovány hodnoty od 0 do 5. V případě zpětné kompatibility v aktuální verzi je dostupná hodnota zobrazení **vwContractActionV0** , která podporuje pouze hodnoty 0 až 2. |
| ContractCodeId                           | int           | No          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractLedgerIdentifier                 | nvarchar (255) | Yes         | Jedinečný identifikátor přidružený k nasazené verzi inteligentního kontraktu pro konkrétní distribuovanou účetní knihu. Například Ethereem. |
| ContractDeployedByUserId                 | int           | No          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Yes         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName           | nvarchar (255) | Yes         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId         | nvarchar (255) | No          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje svou identitu v Azure Active Directory konsorcia.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Yes         | E-mailová adresa uživatele, který smlouvu nasadil |
| WorkflowFunctionId                       | int           | No          | Jedinečný identifikátor pro funkci pracovního postupu |
| WorkflowFunctionName                     | nvarchar (50)  | No          | Název funkce |
| WorkflowFunctionDisplayName              | nvarchar (255) | No          | Název funkce, která se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionDescription              | nvarchar (255) | No          | Popis funkce |
| ContractActionId                         | int           | No          | Jedinečný identifikátor akce kontraktu |
| ContractActionProvisioningStatus         | int           | No          | Určuje aktuální stav procesu zřizování pro akci kontraktu. Možné hodnoty: <br />0 – akce kontraktu byla vytvořena rozhraním API v databázi.<br />1 – byla odeslána akce kontraktu do hlavní knihy.<br />2 – akce kontraktu se úspěšně nasadila do hlavní knihy.<br />3 nebo 4 – smlouvu se nepovedlo nasadit do hlavní knihy.<br />5 – smlouva se úspěšně nasadila do hlavní knihy. <br /><br />Od verze 1,5 jsou podporovány hodnoty od 0 do 5. V případě zpětné kompatibility v aktuální verzi je dostupná hodnota zobrazení **vwContractActionV0** , která podporuje pouze hodnoty 0 až 2. |
| ContractActionTimestamp                  | DateTime (2; 7) | No          | Časové razítko akce kontraktu |
| ContractActionExecutedByUserId           | int           | No          | Jedinečný identifikátor uživatele, který provedl akci kontraktu |
| ContractActionExecutedByUserFirstName    | int           | Yes         | Křestní jméno uživatele, který provedl akci kontraktu |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Yes         | Příjmení uživatele, který provedl akci kontraktu |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Yes         | Externí identifikátor uživatele, který provedl akci kontraktu. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje svou identitu v Azure Active Directory konsorcia. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Yes         | E-mailová adresa uživatele, který provedl akci kontraktu |
| WorkflowFunctionParameterId              | int           | No          | Jedinečný identifikátor pro parametr funkce |
| WorkflowFunctionParameterName            | nvarchar (50)  | No          | Název parametru funkce |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | No          | Název parametru funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionParameterDataTypeId      | int           | No          | Jedinečný identifikátor pro datový typ přidružený k parametru funkce pracovního postupu |
| WorkflowParameterDataTypeName            | nvarchar (50)  | No          | Název datového typu přidruženého k parametru funkce pracovního postupu |
| ContractActionParameterValue             | nvarchar (255) | No          | Hodnota parametru uloženého ve vaší inteligentní smlouvě |
| BlockHash                                | nvarchar (255) | Yes         | Hodnota hash bloku |
| BlockNumber                              | int           | Yes         | Číslo bloku v hlavní knize |
| BlockTimestamp                           | DateTime (2; 7) | Yes         | Časové razítko bloku |
| TransactionId                            | int           | No          | Jedinečný identifikátor pro transakci |
| TransactionFrom                          | nvarchar (255) | Yes         | Strana, která vytvořila transakci |
| TransactionTo                            | nvarchar (255) | Yes         | Strana, která se použila v transakcích |
| TransactionHash                          | nvarchar (255) | Yes         | Hodnota hash transakce |
| TransactionIsWorkbenchTransaction        | bit           | Yes         | Bit, který identifikuje, jestli se jedná o transakci Azure blockchain Workbench |
| TransactionProvisioningStatus            | int           | Yes         | Určuje aktuální stav procesu zřizování pro transakci. Možné hodnoty: <br />0 – transakce byla vytvořena rozhraním API v databázi.<br />1 – transakce byla odeslána do hlavní knihy.<br />2 – transakce se úspěšně nasadila do hlavní knihy.                 |
| TransactionValue                         | desetinné číslo (32, 2) | Yes         | Hodnota transakce |

## <a name="vwcontractproperty"></a>vwContractProperty

Toto zobrazení představuje většinu informací týkajících se vlastností přidružených ke smlouvě a je navržena tak, aby bylo možné snadno usnadnit běžné scénáře vytváření sestav. Pro každou provedenou vlastnost obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti o uživateli, který nasadil pracovní postup
-   Přidružená definice vlastnosti inteligentních kontraktů
-   Specifické hodnoty instance pro vlastnosti
-   Podrobnosti vlastnosti State smlouvy

| Název                               | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                    | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName             | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | bit           | No          | Určuje, jestli je aplikace momentálně povolená.<br />**Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi.                      |
| WorkflowId                         | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                       | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName                | nvarchar (255) | No          | Název pracovního postupu zobrazeného v uživatelském rozhraní |
| WorkflowDescription                | nvarchar (255) | Yes         | Popis pracovního postupu |
| ContractId                         | int           | No          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus         | int           | No          | Určuje aktuální stav procesu zřizování pro kontrakt. Možné hodnoty: <br />0 – kontrakt byl vytvořen rozhraním API v databázi.<br />1 – kontrakt byl odeslán do hlavní knihy.<br />2 – smlouva se úspěšně nasadila do hlavní knihy.<br />3 nebo 4 – smlouvu se nepovedlo nasadit do hlavní knihy.<br />5 – smlouva se úspěšně nasadila do hlavní knihy. <br /><br />Od verze 1,5 jsou podporovány hodnoty od 0 do 5. V případě zpětné kompatibility v aktuální verzi je dostupná hodnota zobrazení **vwContractPropertyV0** , která podporuje pouze hodnoty 0 až 2. |
| ContractCodeId                     | int           | No          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractLedgerIdentifier           | nvarchar (255) | Yes         | Jedinečný identifikátor přidružený k nasazené verzi inteligentního kontraktu pro konkrétní distribuovanou účetní knihu. Například Ethereem. |
| ContractDeployedByUserId           | int           | No          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId   | nvarchar (255) | No          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje svou identitu ve Azure Active Directory konsorcia. |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Yes         | E-mailová adresa uživatele, který smlouvu nasadil |
| WorkflowPropertyId                 | int           |             | Jedinečný identifikátor pro vlastnost pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | No          | ID datového typu vlastnosti |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | Název datového typu vlastnosti |
| WorkflowPropertyName               | nvarchar (50)  | No          | Název vlastnosti pracovního postupu |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | Zobrazovaný název vlastnosti pracovního postupu |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | Popis vlastnosti |
| ContractPropertyValue              | nvarchar (255) | No          | Hodnota vlastnosti u kontraktu |
| StateName                          | nvarchar (50)  | Yes         | Pokud tato vlastnost obsahuje stav kontraktu, jedná se o zobrazované jméno daného stavu. Pokud není přidružen ke stavu, hodnota bude null. |
| StateDisplayName                   | nvarchar (255) | No          | Pokud tato vlastnost obsahuje stav, jedná se o zobrazovaný název stavu. Pokud není přidružen ke stavu, hodnota bude null. |
| StateValue                         | nvarchar (255) | Yes         | Pokud tato vlastnost obsahuje stav, jedná se o stavovou hodnotu. Pokud není přidružen ke stavu, hodnota bude null. |

## <a name="vwcontractstate"></a>vwContractState

Toto zobrazení představuje většinu informací týkajících se stavu konkrétní smlouvy a je navrženo tak, aby bylo možné snadno usnadnit běžné scénáře vytváření sestav. Každý záznam v tomto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti o uživateli, který nasadil pracovní postup
-   Přidružená definice vlastnosti inteligentních kontraktů
-   Podrobnosti vlastnosti State smlouvy

| Název                               | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                    | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName             | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | bit           | No          | Určuje, jestli je aplikace momentálně povolená.<br />**Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi. |
| WorkflowId                         | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                       | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName                | nvarchar (255) | No          | Název zobrazený v uživatelském rozhraní |
| WorkflowDescription                | nvarchar (255) | Yes         | Popis pracovního postupu |
| ContractLedgerImplementationId     | nvarchar (255) | Yes         | Jedinečný identifikátor přidružený k nasazené verzi inteligentního kontraktu pro konkrétní distribuovanou účetní knihu. Například Ethereem. |
| ContractId                         | int           | No          | Jedinečný identifikátor pro kontrakt |
| ContractProvisioningStatus         | int           | No          |Určuje aktuální stav procesu zřizování pro kontrakt. Možné hodnoty: <br />0 – kontrakt byl vytvořen rozhraním API v databázi.<br />1 – kontrakt byl odeslán do hlavní knihy.<br />2 – smlouva se úspěšně nasadila do hlavní knihy.<br />3 nebo 4 – smlouvu se nepovedlo nasadit do hlavní knihy.<br />5 – smlouva se úspěšně nasadila do hlavní knihy. <br /><br />Od verze 1,5 jsou podporovány hodnoty od 0 do 5. V případě zpětné kompatibility v aktuální verzi je dostupná hodnota zobrazení **vwContractStateV0** , která podporuje pouze hodnoty 0 až 2. |
| ConnectionId                       | int           | No          | Jedinečný identifikátor instance blockchain, na kterou je pracovní postup nasazený |
| ContractCodeId                     | int           | No          | Jedinečný identifikátor pro implementaci kódu kontraktu |
| ContractDeployedByUserId           | int           | No          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId   | nvarchar (255) | No          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje svou identitu v Azure Active Directory konsorcia. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Yes         | E-mailová adresa uživatele, který smlouvu nasadil |
| WorkflowPropertyId                 | int           | No          | Jedinečný identifikátor pro vlastnost pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | No          | ID datového typu vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | Název datového typu pro vlastnost pracovního postupu |
| WorkflowPropertyName               | nvarchar (50)  | No          | Název vlastnosti pracovního postupu |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | Zobrazovaný název vlastnosti, která se má zobrazit v uživatelském rozhraní |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | Popis vlastnosti |
| ContractPropertyValue              | nvarchar (255) | No          | Hodnota vlastnosti uložené v kontraktu |
| StateName                          | nvarchar (50)  | Yes         | Pokud tato vlastnost obsahuje stav, zobrazí se název pro daný stav. Pokud není přidružen ke stavu, hodnota bude null. |
| StateDisplayName                   | nvarchar (255) | No          | Pokud tato vlastnost obsahuje stav, jedná se o zobrazovaný název stavu. Pokud není přidružen ke stavu, hodnota bude null. |
| StateValue                         | nvarchar (255) | Yes         | Pokud tato vlastnost obsahuje stav, jedná se o stavovou hodnotu. Pokud není přidružen ke stavu, hodnota bude null. |

## <a name="vwuser"></a>vwUser

Toto zobrazení poskytuje podrobné informace o členech konsorcia zřízených pro používání Azure blockchain Workbench. Ve výchozím nastavení se data naplní počátečním zřizováním uživatele.

| Název               | Typ          | Může mít hodnotu null. | Popis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | No          | Jedinečný identifikátor uživatele |
| ExternalID         | nvarchar (255) | No          | Externí identifikátor pro uživatele. Ve výchozím nastavení je toto ID identifikátor GUID, který představuje ID Azure Active Directory pro uživatele. |
| ProvisioningStatus | int           | No          |Určuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API.<br />1 – klíč byl přidružen k uživateli v databázi.<br />2 – uživatel je plně zřízený. |
| FirstName          | nvarchar (50)  | Yes         | Křestní jméno uživatele |
| LastName           | nvarchar (50)  | Yes         | Poslední jméno uživatele |
| EmailAddress       | nvarchar (255) | Yes         | E-mailová adresa uživatele |

## <a name="vwworkflow"></a>vwWorkflow

Toto zobrazení představuje základní metadata pracovního postupu a také funkce a parametry pracovního postupu. Tato funkce je určená pro vytváření sestav, obsahuje taky metadata týkající se aplikace přidružené k pracovnímu postupu. Toto zobrazení obsahuje data z několika podkladových tabulek, která usnadňují vytváření sestav o pracovních postupech. Pro každý pracovní postup obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Informace o stavu spuštění přidruženého pracovního postupu

| Název                              | Typ          | Může mít hodnotu null. | Description                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                   | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName            | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                | bit           | No          | Určuje, jestli je aplikace povolená. |
| WorkflowId                        | int           | Yes         | Jedinečný identifikátor pracovního postupu |
| WorkflowName                      | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName               | nvarchar (255) | No          | Název zobrazený v uživatelském rozhraní |
| WorkflowDescription               | nvarchar (255) | Yes         | Popis pracovního postupu. |
| WorkflowConstructorFunctionId     | int           | No          | Identifikátor funkce pracovního postupu, který slouží jako konstruktor pro pracovní postup |
| WorkflowStartStateId              | int           | No          | Jedinečný identifikátor stavu |
| WorkflowStartStateName            | nvarchar (50)  | No          | Název stavu |
| WorkflowStartStateDisplayName     | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStartStateDescription     | nvarchar (255) | Yes         | Popis stavu pracovního postupu |
| WorkflowStartStateStyle           | nvarchar (50)  | Yes         | Tato hodnota označuje procento dokončení pracovního postupu v tomto stavu. |
| WorkflowStartStateValue           | int           | No          | Hodnota stavu |
| WorkflowStartStatePercentComplete | int           | No          | Textový popis, který klientovi poskytne nápovědu k tomu, jak tento stav vykreslit v uživatelském rozhraní. Mezi podporované stavy patří *úspěch* a *selhání* . |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Toto zobrazení představuje základní metadata pracovního postupu a také funkce a parametry pracovního postupu. Tato funkce je určená pro vytváření sestav, obsahuje taky metadata týkající se aplikace přidružené k pracovnímu postupu. Toto zobrazení obsahuje data z několika podkladových tabulek, která usnadňují vytváření sestav o pracovních postupech. Pro každou funkci pracovního postupu toto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti funkce pracovního postupu

| Název                                 | Typ          | Může mít hodnotu null. | Description                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName                      | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName               | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                   | bit           | No          | Určuje, jestli je aplikace povolená. |
| WorkflowId                           | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                         | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName                  | nvarchar (255) | No          | Název pracovního postupu zobrazeného v uživatelském rozhraní |
| WorkflowDescription                  | nvarchar (255) | Yes         | Popis pracovního postupu |
| WorkflowFunctionId                   | int           | No          | Jedinečný identifikátor pro funkci |
| WorkflowFunctionName                 | nvarchar (50)  | Yes         | Název funkce |
| WorkflowFunctionDisplayName          | nvarchar (255) | No          | Název funkce, která se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionDescription          | nvarchar (255) | Yes         | Popis funkce pracovního postupu |
| WorkflowFunctionIsConstructor        | bit           | No          | Určuje, jestli je funkce pracovního postupu konstruktorem pracovního postupu. |
| WorkflowFunctionParameterId          | int           | No          | Jedinečný identifikátor pro parametr funkce |
| WorkflowFunctionParameterName        | nvarchar (50)  | No          | Název parametru funkce |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | No          | Název parametru funkce, který se má zobrazit v uživatelském rozhraní |
| WorkflowFunctionParameterDataTypeId  | int           | No          | Jedinečný identifikátor pro datový typ přidružený k parametru funkce pracovního postupu |
| WorkflowParameterDataTypeName        | nvarchar (50)  | No          | Název datového typu přidruženého k parametru funkce pracovního postupu |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Toto zobrazení představuje vlastnosti definované pro pracovní postup. Pro každou vlastnost obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti vlastnosti pracovního postupu

| Název                         | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName              | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName       | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled           | bit           | No          | Určuje, jestli je aplikace momentálně povolená.<br />**Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi. |
| WorkflowId                   | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                 | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName          | nvarchar (255) | No          | Název, který se má zobrazit pro pracovní postup v uživatelském rozhraní |
| WorkflowDescription          | nvarchar (255) | Yes         | Popis pracovního postupu |
| WorkflowPropertyID           | int           | No          | Jedinečný identifikátor pro vlastnost pracovního postupu |
| WorkflowPropertyName         | nvarchar (50)  | No          | Název vlastnosti |
| WorkflowPropertyDescription  | nvarchar (255) | Yes         | Popis vlastnosti |
| WorkflowPropertyDisplayName  | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní |
| WorkflowPropertyWorkflowId   | int           | No          | ID pracovního postupu, ke kterému je přidružena tato vlastnost |
| WorkflowPropertyDataTypeId   | int           | No          | ID datového typu definovaného pro vlastnost |
| WorkflowPropertyDataTypeName | nvarchar (50)  | No          | Název datového typu definovaného pro vlastnost |
| WorkflowPropertyIsState      | bit           | No          | Toto pole označuje, jestli tato vlastnost pracovního postupu obsahuje stav pracovního postupu. |

## <a name="vwworkflowstate"></a>vwWorkflowState

Toto zobrazení představuje vlastnosti přidružené k pracovnímu postupu. Pro každou kontrakt obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Informace o stavu pracovního postupu

| Název                         | Typ          | Může mít hodnotu null. | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | Jedinečný identifikátor aplikace |
| ApplicationName              | nvarchar (50)  | No          | Název aplikace |
| ApplicationDisplayName       | nvarchar (255) | No          | Popis aplikace |
| ApplicationEnabled           | bit           | No          | Určuje, jestli je aplikace momentálně povolená.<br />**Poznámka:** I když se aplikace může v databázi projevit jako zakázaná, přidružené smlouvy zůstanou na blockchain a data o těchto smlouvách zůstanou v databázi. |
| WorkflowId                   | int           | No          | Jedinečný identifikátor pracovního postupu |
| WorkflowName                 | nvarchar (50)  | No          | Název pracovního postupu |
| WorkflowDisplayName          | nvarchar (255) | No          | Název zobrazený v uživatelském rozhraní pracovního postupu |
| WorkflowDescription          | nvarchar (255) | Yes         | Popis pracovního postupu |
| WorkflowStateID              | int           | No          | Jedinečný identifikátor stavu |
| WorkflowStateName            | nvarchar (50)  | No          | Název stavu |
| WorkflowStateDisplayName     | nvarchar (255) | No          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStateDescription     | nvarchar (255) | Yes         | Popis stavu pracovního postupu |
| WorkflowStatePercentComplete | int           | No          | Tato hodnota označuje procento dokončení pracovního postupu v tomto stavu. |
| WorkflowStateValue           | nvarchar (50)  | No          | Hodnota stavu |
| WorkflowStateStyle           | nvarchar (50)  | No          | Textový popis, který klientovi poskytne nápovědu k tomu, jak tento stav vykreslit v uživatelském rozhraní. Mezi podporované stavy patří *úspěch* a *selhání* . |
