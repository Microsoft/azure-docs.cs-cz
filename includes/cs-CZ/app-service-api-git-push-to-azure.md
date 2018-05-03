Pomocí rozhraní příkazového řádku Azure získejte adresu URL pro vzdálené nasazení aplikace API. V následujícím příkazu nahraďte *\<app_name>* názvem webové aplikace.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Nakonfigurujte místní nasazení z Gitu tak, aby bylo možné ho uložit do vzdáleného umístění.

```bash
git remote add azure <URI from previous step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure. Zobrazí se výzva k zadání hesla, které jste zadali dříve při vytváření uživatele nasazení. Dejte pozor na to, abyste zadali heslo, které jste si vytvořili dříve v rychlém startu, a ne heslo, se kterým se přihlašujete k portálu Azure Portal.

```bash
git push azure master
```
