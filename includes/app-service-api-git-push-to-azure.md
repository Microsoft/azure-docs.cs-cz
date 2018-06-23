Pomocí rozhraní příkazového řádku Azure získejte adresu URL pro vzdálené nasazení aplikace API. V následujícím příkazu nahraďte *\<app_name>* názvem webové aplikace.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Nakonfigurujte místní nasazení z Gitu tak, aby bylo možné ho uložit do vzdáleného umístění.

```bash
git remote add azure <URI from previous step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure. Zobrazí se výzva k zadání hesla, které jste zadali dříve při vytváření uživatele nasazení. Ujistěte se, abyste zadali heslo, které jste vytvořili v dříve v rychlé spuštění a není heslo, které používáte pro přihlášení k portálu Azure.

```bash
git push azure master
```
