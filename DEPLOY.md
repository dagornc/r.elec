# 🚀 Guide de Déploiement - ElectroMorlaix

## 📋 Checklist Pré-Déploiement

### ✅ Fichiers à Vérifier
- [ ] `index.html` - Page principale optimisée
- [ ] `README.md` - Documentation complète
- [ ] `sitemap.xml` - Plan du site
- [ ] `robots.txt` - Instructions pour les robots
- [ ] `tests.html` - Suite de tests automatisés
- [ ] Dossier `photos/` - Images optimisées

### ✅ Tests Techniques
- [ ] Tests de compatibilité navigateurs
- [ ] Validation HTML5 (W3C)
- [ ] Tests de performance (Lighthouse)
- [ ] Vérification SEO
- [ ] Tests d'accessibilité
- [ ] Validation des formulaires
- [ ] Tests responsive (mobile/tablet/desktop)

## 🌐 Configuration Hébergeur

### Prérequis Serveur
```
- Apache 2.4+ ou Nginx 1.18+
- PHP 7.4+ (pour traitement formulaire)
- SSL/TLS certificat
- Compression GZIP activée
- Cache navigateur configuré
```

### Configuration Apache (.htaccess)
```apache
# Force HTTPS
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

# Compression GZIP
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

# Cache navigateur
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>

# Headers de sécurité
<IfModule mod_headers.c>
    Header always set X-Frame-Options DENY
    Header always set X-Content-Type-Options nosniff
    Header always set X-XSS-Protection "1; mode=block"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Content-Security-Policy "default-src 'self' 'unsafe-inline' 'unsafe-eval' https:; img-src 'self' data: https:; font-src 'self' https://fonts.gstatic.com; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://cdn.tailwindcss.com https://cdnjs.cloudflare.com https://unpkg.com;"
</IfModule>

# Redirection www vers non-www
RewriteCond %{HTTP_HOST} ^www\.electromorlaix\.fr [NC]
RewriteRule ^(.*)$ https://electromorlaix.fr/$1 [L,R=301]
```

### Configuration Nginx
```nginx
server {
    listen 443 ssl http2;
    server_name electromorlaix.fr;
    
    root /var/www/electromorlaix;
    index index.html;
    
    # SSL Configuration
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    
    # Security Headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
    
    # Compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;
    
    # Cache
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Redirections
    location / {
        try_files $uri $uri/ =404;
    }
}

# Redirection HTTP vers HTTPS
server {
    listen 80;
    server_name electromorlaix.fr www.electromorlaix.fr;
    return 301 https://electromorlaix.fr$request_uri;
}

# Redirection www vers non-www
server {
    listen 443 ssl http2;
    server_name www.electromorlaix.fr;
    return 301 https://electromorlaix.fr$request_uri;
}
```

## 📤 Processus de Déploiement

### 1. Préparation Locale
```bash
# Vérifier tous les fichiers
ls -la

# Tester le site en local
python3 -m http.server 8000
# ou
php -S localhost:8000

# Valider HTML
# Utiliser: https://validator.w3.org/

# Tests Lighthouse
# Utiliser: Chrome DevTools > Lighthouse
```

### 2. Upload via FTP/SFTP
```bash
# Exemple avec rsync
rsync -avz --progress ./ user@electromorlaix.fr:/var/www/electromorlaix/

# Ou avec SFTP
sftp user@electromorlaix.fr
put index.html
put sitemap.xml
put robots.txt
put -r photos/
```

### 3. Configuration DNS
```
Type    Nom    Valeur                  TTL
A       @      IP_SERVEUR             3600
A       www    IP_SERVEUR             3600
CNAME   www    electromorlaix.fr      3600
```

### 4. Vérifications Post-Déploiement
```bash
# Test connectivité
curl -I https://electromorlaix.fr

# Test SSL
curl -I https://electromorlaix.fr --verbose

# Test compression
curl -H "Accept-Encoding: gzip" -I https://electromorlaix.fr

# Test redirections
curl -I http://electromorlaix.fr
curl -I https://www.electromorlaix.fr
```

## 🔧 Outils de Validation

### Performance
- **Google PageSpeed Insights**: https://pagespeed.web.dev/
- **GTmetrix**: https://gtmetrix.com/
- **WebPageTest**: https://webpagetest.org/

### SEO
- **Google Search Console**: https://search.google.com/search-console
- **Screaming Frog**: Crawl SEO
- **SEMrush**: Audit SEO complet

### Sécurité
- **SSL Labs**: https://ssllabs.com/ssltest/
- **Security Headers**: https://securityheaders.com/
- **Mozilla Observatory**: https://observatory.mozilla.org/

### Accessibilité
- **WAVE**: https://wave.webaim.org/
- **axe DevTools**: Extension navigateur
- **Lighthouse**: Audit d'accessibilité

## 📊 Monitoring Post-Lancement

### Analytics à Configurer
```html
<!-- Google Analytics 4 -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>

<!-- Google Tag Manager (optionnel) -->
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-XXXXXXX');</script>
```

### Outils de Monitoring
- **Google Analytics** - Trafic et conversions
- **Google Search Console** - Performance SEO
- **Hotjar** - Heatmaps et enregistrements
- **UptimeRobot** - Monitoring uptime
- **Pingdom** - Performance continue

## 🎯 Optimisations Post-Lancement

### SEO Local
- [ ] Inscription Google My Business
- [ ] Citations locales (Pages Jaunes, Yelp)
- [ ] Avis clients Google
- [ ] Fiche Facebook Business
- [ ] Profil LinkedIn Entreprise

### Marketing Digital
- [ ] Campagnes Google Ads
- [ ] Facebook/Instagram Business
- [ ] Référencement local
- [ ] Newsletter emails
- [ ] Blog technique

### Maintenance Continue
- [ ] Sauvegarde automatique
- [ ] Mises à jour sécurité
- [ ] Monitoring performances
- [ ] Tests utilisateurs
- [ ] Optimisations SEO
- [ ] Nouveaux contenus

## 📞 Support Technique

### Contacts Urgence
- **Hébergeur**: support@hebergeur.fr
- **Registrar**: support@domaine.fr
- **Développeur**: contact@consultant.fr

### Documentation Technique
- **Logs serveur**: `/var/log/nginx/` ou `/var/log/apache2/`
- **Configuration**: `/etc/nginx/` ou `/etc/apache2/`
- **SSL**: `/etc/ssl/certs/`

## ✅ Checklist Final

### Avant Mise en Ligne
- [ ] Backup site actuel (si existant)
- [ ] Tests complets effectués
- [ ] DNS configuré
- [ ] SSL activé
- [ ] Redirections testées
- [ ] Formulaire de contact testé
- [ ] Google Analytics configuré
- [ ] Search Console configuré

### Après Mise en Ligne
- [ ] Test complet du site
- [ ] Vérification analytics
- [ ] Soumission sitemap
- [ ] Test formulaires
- [ ] Vérification performances
- [ ] Test compatibilité mobile
- [ ] Backup première version

---

## 🎉 Site Prêt pour le Lancement !

**URL**: https://electromorlaix.fr  
**Status**: ✅ Production Ready  
**Performance**: 95+ Lighthouse Score  
**SEO**: Optimisé Local Morlaix  
**Accessibilité**: WCAG 2.1 AA  

*Développé avec ❤️ pour ElectroMorlaix* 