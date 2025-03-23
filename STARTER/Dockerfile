FROM ruby:3.3.7
RUN apt-get update -qq
RUN curl https://deb.nodesource.com/setup_lts.x | bash - && apt-get install -y nodejs
RUN npm install --global yarn

RUN gem install rails -v 8.0.2

WORKDIR /${PROJECT_NAME}
COPY Gemfile Gemfile.lock /${PROJECT_NAME}/
RUN bundle install
CMD ["rails", "server", "-b", "0.0.0.0"]