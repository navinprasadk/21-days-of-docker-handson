FROM nginx:alpine
HEALTHCHECK --interval=25s --timeout=5s CMD curl --fail http://localhost:80/ || exit 1
EXPOSE 80