FROM debian:10.12-slim

# Create a non-root user and group named "navin" with userid and groupid "1000"
RUN groupadd --gid 1000 navin \
    && useradd --uid 1000 --gid 1000 -m navin

# Instructing docker to run the conatiner as non-root user
USER navin
