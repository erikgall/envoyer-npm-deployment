# Asset Compilation w/ Laravel Envoyer

## The Problem(s)

1. Running `gulp --production` before deployment is tedious and often forgotten, especialy when you are running gulp watch and assets are being compiled after any change.
2. Running `npm install` on a server during every deployment and compiling it after is slow.
3. Compiling locally and committing your versioned assets doesn't allow you to make tiny commits unless you leave out the versioned file until you are done.
4. Using Envoyer for zero-downtime deployments doesn't handle this for you.

## The Solution

Using Envoyer's deployment hooks we can fix the problems so that our assets are compiled when the project is deployed. We can also eliminate how long it takes NPM to download all of the packages we need by storing them in a shared node_modules folder.

Below are the steps required to download your node dependencies and compile your assets during deployment.

## Performance

After the deployment hook runs for the first time, the deployment time drastically decreases. This is because we need to download all the node packages during the first deployment and then from there we symlink the project's node_modules directory to the release's node_modules directory.

1. Running NPM install and gulp on server w/out symlinks (see example below): 4 min
2. Using .envoyer deployment hook (first deployment): 2 min
3.  Using .envoyer deployment hook (after first deployment): 55s

### Example w/out symlinks

Using one deployment hook containing:
```bash
npm install
gulp --production
```

## Usage


1. Login to your envoyer.io account and go to your projects deployment screen.
2. Click "Deployment Hooks" tab
3. Click cog wheel icon/button on the "Activate New Release" hook
4. In the "Before This Action" panel click add hook
5. Add a hook name
6. Add the user to run the hook as (default: forge)
7. Copy and paste the .envoyer file into the editor*

## The Psuedo-Code Steps

1. Go to the release directory
2. Copy the package.json file to the projects directory
3. Go to the project directory
4. Run `npm install`
5. Run `npm update`
6. Symlink the {{project}}/node_modules directory to the {{release}}/node_modules directory
7. Go to the release directory
8. Run gulp

## My Hooks*

*I broke down the steps into 5 different hooks so I could see how long certain steps were taking. The hooks names are listed below.*

1. Copy package.json
2. Install node packages
3. Update node packages
4. Create node_modules symlink
5. Compile assets
