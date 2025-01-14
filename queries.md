# Queries Utilizadas na Migração

## Verificação e Limpeza do Banco

### Verificar URLs no Banco
```sql
-- Verificar URLs configuradas
SELECT option_name, option_value 
FROM zop_options 
WHERE option_name IN ('siteurl', 'home');
```

### Atualização de URLs
```sql
-- Atualizar URLs do site antigo para novo
UPDATE zop_options 
SET option_value = 'https://novo-dominio.com' 
WHERE option_name IN ('siteurl', 'home');

-- Atualizar URLs em posts e páginas
UPDATE zop_posts 
SET guid = REPLACE(guid, 'dominio-antigo.com', 'novo-dominio.com'),
    post_content = REPLACE(post_content, 'dominio-antigo.com', 'novo-dominio.com') 
WHERE guid LIKE '%dominio-antigo.com%' 
   OR post_content LIKE '%dominio-antigo.com%';
```

### Limpeza de Dados de Plugins
```sql
-- Remover referências do Weglot
DELETE FROM zop_options 
WHERE option_name LIKE '%weglot%';

DELETE FROM zop_postmeta 
WHERE meta_key LIKE '%weglot%';
```

### Verificações de Segurança
```sql
-- Verificar estrutura de usuários
SELECT meta_key, meta_value 
FROM zop_usermeta 
WHERE user_id = 1 
AND meta_key LIKE '%capabilities%';

-- Configurar permissões de novo administrador
INSERT INTO zop_usermeta (user_id, meta_key, meta_value) 
VALUES (2, 'zop_capabilities', 'a:1:{s:13:"administrator";b:1;}');
```
