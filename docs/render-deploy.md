# API Cliente no Render

O Blueprint `render.yaml` prepara um Web Service Docker gratuito para o primeiro teste. Ele não contém valores privados e solicita as variáveis marcadas com `sync: false` durante a criação. O deploy automático por commit fica desativado; atualizações devem ser iniciadas manualmente no Render.

## Primeiro deploy

1. Conecte este repositório e selecione a versão aprovada que contém `render.yaml`.
2. Informe as configurações privadas existentes de PostgreSQL, Firebase, JWT e criptografia. Preserve o emissor e as chaves já usados nos dados existentes.
3. Confirme que o PostgreSQL aceita acesso a partir da região do Render e valide a conexão SSL.
4. O Blueprint define `SERVER_PORT=10000`. O Render encaminha as requisições para a porta configurada no serviço.
5. Confira o build, a memória de até 512 MB e o acesso HTTP; depois valide perfil e catálogo com uma conta autenticada.

O endpoint `/v3/api-docs` disponível sem autenticação pode confirmar resposta HTTP, mas não confirma conectividade com o banco. O Render pode usar a verificação TCP padrão até existir uma rota HTTP de saúde própria.

## Avisos

No primeiro deploy, `CLIENT_NOTIFICATIONS_ENABLED` e `CUSTOMER_NOTIFICATION_DISPATCH_ENABLED` ficam `false`, e `CUSTOMER_NOTIFICATION_ALLOW_INSECURE` fica `false`. Avisos e emissão de token para avisos ficam indisponíveis durante essa etapa.

`CUSTOMER_NOTIFICATION_REDIS_URL` não é preenchida com o endereço Docker local. Quando a API Redis Cliente estiver publicada, cadastre sua URL base HTTPS real, sem usuário, senha, caminho adicional, query ou fragmento. Configure também `CLIENT_NOTIFICATIONS_JWT_SECRET`, `CLIENT_NOTIFICATIONS_COMPANY_ID`, `CLIENT_NOTIFICATIONS_TTL_SECONDS` e `NOTIFICATION_PUBLISHER_SECRET` com valores compatíveis com a API Redis.

Depois de validar Redis e permissões no banco, atualize o Blueprint para habilitar `CLIENT_NOTIFICATIONS_ENABLED` e `CUSTOMER_NOTIFICATION_DISPATCH_ENABLED`. O Blueprint controla esses valores e pode reaplicá-los em uma sincronização; alterar apenas o painel não substitui a atualização do arquivo. Mantenha `CUSTOMER_NOTIFICATION_ALLOW_INSECURE=false`.

Antes de habilitar o disparo em nuvem, confirme que a instância local deixou de disparar para o mesmo banco. Teste os destinatários, as regras de prioridade e o limite diário.

## Configurações opcionais

`CUSTOMER_DATA_DECRYPTION_KEYS` só precisa ser configurada se for necessário ler dados protegidos por versões anteriores da chave. `CUSTOMER_DATA_KEY_VERSION` deve refletir a configuração existente.

As variáveis SMTP e `CUSTOMER_PASSWORD_RECOVERY_URL` pertencem ao fluxo legado de recuperação; o aplicativo usa recuperação Firebase. O plano gratuito do Render bloqueia portas SMTP comuns.

Nunca inclua `.env`, senhas, segredos ou chaves privadas em commits, argumentos do build Docker ou mensagens de log.
